---
title: "Are You Building Your App Right?"
date: 2019-07-25T23:29:21+05:30
draft: false
author: "Cyril Pillai"
tags:
  - Mobile Apps
  - Android
  - Kotlin
image: /images/blogs/mobile_app_essentials/cover.webp
description: ""
toc:
---

If you’re reading this, you’re probably interested in building mobile apps. I am, too. I’ve worked on a variety of mobile apps, and have come to the realisation that there are a few things that you have to integrate in an app no matter what it does and which domain it caters to.

This series post pens down some essentials you should consider integrating into every app you build. Here we go:

## 1. Crash Reporting

<p align="center">
    <img src="/images/blogs/mobile_app_essentials/inevitable.webp">
</p>

Anyone who develops software will confirm that bugs are indeed, inevitable.

> ###### ***A bug free software is a myth.***

As developers, we are in a never ending battle to eliminate bugs from the system. We may not realise it at the time, but the code we write and the decisions we make can have serious consequences (like [Y2K](https://en.wikipedia.org/wiki/Year_2000_problem), [Boeing 737 Max 8](https://blog.cleancoder.com/uncle-bob/2019/05/18/737-Max-8.html), [Metric Mishap](https://en.wikipedia.org/wiki/Mars_Climate_Orbiter#Cause_of_failure) in NASA’s Mars Orbiter, etc).

Thus, we need to make sure that we have a robust infrastructure for finding the flaws in the software we build. At Gojek, we use [Firebase Crashlytics](https://firebase.google.com/products/crashlytics/) which gives an extensive crash report with complete stack trace, number of crashes, device info, geographic info, etc.

**Useful Tools: [Firebase Crashlytics](https://firebase.google.com/products/crashlytics/), [Instabug](https://www.instabug.com/), [Sentry](https://sentry.io/welcome/), [BugSnag](https://www.bugsnag.com/), etc.**

## 2. Analytics

We build software to cater to users’ needs. Hence, the evolution of the product depends on the user. We may need to track a user’s behavioural data — screen time, button clicks, navigation patterns, etc. — to understand the user better. This data can then be used to tweak the product by enhancing the UI/UX, resulting in a richer experience.

Apart from user interaction data, logging non-fatal errors and API failures can be extremely helpful in enhancing the app experience too.

> ###### Logging events is just a piece of the puzzle though.

Monitoring the events and using the data to improve user experience is more important. For example, monitoring the number of users on a particular version of the app can help decide whether a specific feature can be deprecated or not.

At Gojek, we use [CleverTap](https://clevertap.com/) & [AppsFlyer](https://www.appsflyer.com/) to capture user events. We use many tools for analysis as well.

**Useful Tools: [Google Analytics](https://firebase.google.com/docs/analytics), [CleverTap](https://clevertap.com/), [AppsFlyer](https://www.appsflyer.com/), [MixPanel](https://mixpanel.com/), [Amplitude](https://amplitude.com/), [Segment](https://segment.com/), [Metabase](https://www.metabase.com/), etc.**

## 3. Feature Toggle

***Picture this***

```
→ You introduce a new feature in your app.
→ Your app’s crash free sessions reduce drastically after a few
days of releasing it to App Store.
→ You debug and figure out that the new feature has an issue.
→ Now, you have to fix the issue as quickly as possible and release
another update to App Store — which will take its own sweet time to
review it (Problems of being a mobile dev 😞).
→ In the time it took for you to fix the issue and for the App Store
to release the new update, the damage was already done.
```

### A few examples of the damage

Crash free sessions went down, App Store ratings sunk 📉, App reviews section got filled with complaints, Uninstalls increased, Users started bombarding the customer care unit, Product got defamed on social media, Management folks got upset 💢

> ###### What can we do for Damage Control?

Well, we can avoid all this with [feature toggles/feature flags](https://en.wikipedia.org/wiki/Feature_toggle). At Gojek, we don’t release a new feature without a feature toggle. This ensures we can toggle it off immediately if things go south ([Murphy’s Law](https://en.wikipedia.org/wiki/Murphy%27s_law) exists for a reason 😑). We’ve been using [Firebase Remote Config](https://firebase.google.com/docs/remote-config) for implementing feature toggles, which suffices for basic use cases, but we’re moving away from it to an in-house tool called [Litmus](https://medium.com/gojekengineering/introducing-litmus-gojeks-own-experimentation-platform-3803467b6a53) which is built to cater to our specific needs and gives more granular control over feature rollouts. You can read more about Litmus here:

**More Useful Tools: [Firebase Remote Config](https://firebase.google.com/docs/remote-config), [Split](https://www.split.io/), [Rollout](https://www.cloudbees.com/capabilities/feature-management), etc.**

## 4. Globalisation

Like other products, software shouldn’t be built with a small target audience in mind. It should have the ability to scale up and cater to a wide variety of users across the globe.

Globalisation has two phases: [internationalisation & localisation](https://en.wikipedia.org/wiki/Internationalization_and_localization).

> Internationalisation (i18n) refers to the initial setup of the software in a way that it can adapt to various languages and regions. It is a one-time process.

Localisation (l10n) refers to the actual implementation efforts to support a particular language/region.

As Gojek is available in multiple countries (Indonesia, Singapore, [Vietnam](https://medium.com/gojekengineering/good-morning-vietnam-how-gojek-went-international-c7c302cc8caa), and [Thailand](https://medium.com/gojekengineering/the-final-chapter-how-gojek-went-international-7839ebc48db5), we need to make sure that all of our users get a familiar and localised experience based on their geographic location and language preferences. Our mobile apps have localised copies and illustrations and our APIs return data based on the user’s locale.

**Useful Tools: [OneSky](https://www.oneskyapp.com/), [Lokalise](https://lokalise.com/), etc.**

## 5. Force Update

<p align="center">
    <img src="/images/blogs/mobile_app_essentials/not_today.webp">
</p>

Any non-trivial app will evolve throughout its lifecycle — changing with every iteration. App updates are the most crucial part of this evolution.

Updates are released for a multitude of reasons:

> ###### First release, new feature, design overhaul, bug fixes, enhancements, it’s a long list.

<p align="center">
    <img src="/images/blogs/mobile_app_essentials/update.webp">
</p>

The problem with App updates is that the user can opt out and continue using the older versions of the app, which means:

> ###### No breaking changes ever, backward compatibility for every version of the app, users missing out on new features, hindrance in adoption of crucial bug fixes… it’s another long list.

**Force Update** is a mechanism to enforce timely updates by restricting app usage. One important point to keep in mind is to use this sparingly, as it’s blocking the user from using the app, and might increase the churn rate.

Force Update should be integrated in the app from the get-go. Its implementation is pretty straightforward and there are various ways to do it.

You could use [Firebase Remote Config](https://firebase.google.com/docs/remote-config) to get the version identifiers of the latest version and do a comparison with the installed one.

Another approach would be to make an API call to a Backend service with the current version identifiers and act upon the response from the service.

**Useful Tools: [Google’s In-app Updates](https://developer.android.com/guide/playcore/in-app-updates).**

## 6. Design Language

> ###### Looks aren’t everything.

This is true in most cases, but not when it comes to mobile apps. In our world, user interface/user experience is everything. You should have a consistent user experience throughout the app to make sure the user gets familiar with the app as quickly as possible. It helps to follow some kind of Design Language while building mobile apps. Having a set of components and similar theming also helps in reusing a lot of code while building the app.

Gojek has its own Design Language called [Asphalt](https://asphalt.gojek.io/). If you want to know more about how we ended up building it, check out [this](https://medium.com/gojekengineering/of-dreams-deadlines-and-a-design-system-6ad7c3d1b17f) blog. Also, did you notice Gojek has a new design style? Read about it [here](https://medium.com/gojekengineering/a-new-gojek-4100c27875ff).)

**Useful Tools: [Material Design](https://m3.material.io/), [Flat Design](https://en.wikipedia.org/wiki/Flat_design), [Asphalt](https://asphalt.gojek.io/), etc.**

That’s all from me for now, folks. But there’s a lot more to talk about on this subject —  keep watching this space for more.

---

*Cross posted on [Gojek Engineering](https://www.gojek.io/blog/are-you-building-your-app-right)*

























