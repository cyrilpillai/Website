---
title: "Expecting the Exceptions"
date: 2018-07-25T23:29:21+05:30
draft: false
author: "Cyril Pillai"
tags:
  - Exceptions
  - Android
  - Kotlin
image: /images/blogs/expecting_exceptions/cover.webp
description: ""
toc:
---

## Error Format
Most of the APIs have a defined format in which they return errors. This contract helps the API consumers in handling the errors.

At m.Paani, we too have a contract for raising errors. The API sends a JSON list containing N error objects. A typical error response would look something like

```json
[
    {
        "field": "non_field_errors",
        "code": 4005,
        "client_message": "Please try logging in again.",
        "server_message": "Token Expired at 2018-07-11T10:30:00Z"
    }
]
```

**field**: Contains name of the field only if it’s a field error else returns non_field_errors

**code**: Internal Error Codes (as most non trivial systems have a lot of domain specific errors and edge cases). This also provides granularity and makes debugging a bit easier.

> Consider this - API returns an HTTP 401 UNAUTHORIZED, it’s impossible to identify the actual cause. This could have been raised because token was invalid or expired or perhaps it wasn’t sent in the first place.

**client_message**: A user friendly message that can be directly shown to the user.

**server_message**: A message containing more technical information that can help in debugging the issue.

---

## Handling the error

Now that we know the format of error we’re dealing with, let’s dive into handling it at the Android side.

Here’s the data class which would be used to deserialize the error body.

```kotlin
data class NetworkError(
        @SerializedName("code") val code: Int,
        @SerializedName("field") val field: String,
        @SerializedName("server_message") val serverMessage: String,
        @SerializedName("client_message") val clientMessage: String
)
```

Here’s an example exception which would be raised once a corresponding JSON error is encountered.

```kotlin
data class AuthTokenException(
    val reason: String = "Authentication Token is invalid"
): Throwable(message = reason)
```

This is how you would be handling errors in your API calls.

```kotlin
webService.getUserDetails()
.handleErrors(gson) {
    //Handle API-specific errors
    val list = mutableListOf<Throwable>()
    it.forEach {
        when (it.code) {
            2081 -> list.add(UserDeactivatedException(it.clientMessage))
        }
    }
    return@handleErrors list
}
.subscribe({
    //You can finally go to bed now
    Log.d(TAG, "success: $it")
}, {
    //Get some more coffee and handle this error
    it.printStackTrace()
})
```

<p align="center">
    <img src="/images/blogs/expecting_exceptions/confusion.webp">
</p>

## Where magic

Let’s get to the interesting part, shall we?

```kotlin
/**
 * Extension function for [Single] that adds error handling logic to handle Network Errors in the stream
 * This extension function is sufficient for all Network Requests as all of the Retrofit APIs should
 * return a [Single]. But it can be extended for Observable/Flowable etc., if need be.
 *
 * Loops through the whole list and adds all of the recognizable errors as exceptions
 * and at the same time makes sure the unknown/API-specific errors are passed on to the
 * [customErrors] lambda function for processing.
 * The lambda function receives only the unhandled errors and not the whole list. This is a
 * small optimization that makes sure [customErrors] does not have to loop through already handled
 * errors.
 *
 * @param gson [Gson] instance to pass it to [parseError]
 * @param customErrors Lambda function which receives a list of [NetworkError] and is expected to
 * handle the API-specific errors and return a list of [Throwable]
 * @return [Single] that invokes [Single.error] with a [CompositeException] containing exceptions
 * based on all of the errors returned by the API (Returns the original exception if it's not able
 * to parse it).
 */
inline fun <T> Single<T>.handleErrors(gson: Gson, crossinline customErrors: (List<NetworkError>) ->
List<Throwable> = { listOf() }): Single<T> = this.onErrorResumeNext { e ->
    val errors: MutableList<Throwable> = mutableListOf()
    if (e is HttpException) {
        when (e.code()) {
            in 400..499 -> {
                val errorList = e.response().errorBody().parseError(gson = gson)
                val unhandledErrorList: MutableList<NetworkError> = mutableListOf()
                errorList.forEach {
                    when (it.code) {
                        4001 -> errors.add(AuthTokenException(it.clientMessage))
                        4004 -> errors.add(NotFoundException(it.clientMessage))
                        2001, 2031 -> errors.add(EmptyFieldException(it.field))
                        else -> unhandledErrorList.add(it)
                    }
                }
                errors.addAll(customErrors(unhandledErrorList))
            }
        }
    }
    if (errors.isEmpty()) errors.add(e)
    return@onErrorResumeNext Single.error(CompositeException(errors))
}

/**
 * Extension function for [ResponseBody] that converts the [ResponseBody.string] into
 * [List] of [NetworkError]
 * *Note: Returns empty list if gson fails to parse the [ResponseBody.string]
 * @param gson [Gson] instance to deserialize error response
 * @return [List] of [NetworkError]
 */
fun ResponseBody?.parseError(gson: Gson): List<NetworkError> = try {
    gson.fromJson<List<NetworkError>>(this?.string(), object :
            TypeToken<List<NetworkError>>() {}.type)
} catch (e: Exception) {
    listOf()
}
```

**handleErrors()** is an [extension function](https://kotlinlang.org/docs/extensions.html) written in Kotlin that can be used on any **Single**.

It has a very straight forward responsibility

> To transform the error response into a list of NetworkError and map every item to the corresponding Exception and return a [CompositeException](http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/rxjava3/exceptions/CompositeException.html).

RxJava’s **onErrorResumeNext** is the backbone of this extension function.

If you’ve never heard of this operator before, I would highly recommend reading a bit about it [here](http://reactivex.io/RxJava/javadoc/rx/Observable.html#onErrorResumeNext-rx.Observable-), but just to summarize what it does

> It replaces the current stream with a new Observable as soon as an error is encountered.

The function is pretty self explanatory but a higher level breakdown would be

- Hijack the thrown exception.
- Continue only if it’s an **HttpException** else just return a new **Single.error()** with the encountered exception to make sure the subscriber’s **onError** is called.
- Now if it is an **HttpException**, then convert the response body into a **List\<NetworkError\>** using another extension function called **parseError()**.
- Iterate over the list of errors and add their corresponding exceptions to the **errorList**. This basically means handling all of the BASE errors like Not Found Exceptions, Auth Exceptions, Empty Field Exceptions, etc.
- Add all of the unknown errors into the **unhandledErrorList** and pass it to the lambda function received. Most of the APIs would have unique errors which cannot/should not be handled by this function. It should rather be delegated to the caller.
- Based on the function contract, the lambda function would then process the unhandledErrorList and return a **List\<Throwable\>** containing all of the API specific exceptions.
- Add the new exceptions to the **errorList** and return a new **Single.error()** with the **CompositeException** containing all of the modified exceptions.
- The subscriber can then iterate over the **`CompositeException`** and act accordingly.

What do you think about this approach. Feel free to share your thoughts.

---

*Cross posted in [mPaani Engineering](https://medium.com/mpaani/expecting-the-exceptions-b6e6820160fc)*