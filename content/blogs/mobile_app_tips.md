---
title: "Six Tips to Build Great Mobile Apps"
date: 2019-08-22T23:29:21+05:30
draft: false
author: "Cyril Pillai"
tags:
  - Mobile Apps
  - Android
  - Kotlin
image: /images/blogs/mobile_app_tips/cover.webp
description: ""
toc:
---

This post is a follow up to [Are You Building Your App Right?](../mobile_app_essentials) If you haven’t read that one, I highly recommend going through it first:

Now that you’re all caught up, let’s dive back into what you need to build a great app.

## 1. Graphics

> ###### A picture is worth a thousand words

Most of the apps in this day and age are driven by graphics. Ingenious graphical illustrations and visual elements in products make them pleasing to use.

> Confession: I sometimes play Chrome’s Downasaur (type *chrome://dino/*) even when I have internet 😅.

This means we have to give extra attention to our illustrations and setup a good infrastructure for delivering images to the app. A [CDN](https://en.wikipedia.org/wiki/Content_delivery_network) is mandatory for this purpose — to make sure users see these images as soon as possible.

As most apps show a preview before showing a full-blown image, it’s also useful to cache multiple variants of a single image based on different sizes like thumbnails as well as small, medium, and large versions. Alternatively, you can use something more sophisticated — like a URL-based image manipulation service.

**Useful Tools: [Amazon S3](https://aws.amazon.com/s3/), [Amazon CloudFront](https://aws.amazon.com/cloudfront/[), [Cloudinary](https://cloudinary.com/), [ImageKit](https://imagekit.io/), etc.**

## 2. App Versioning

As apps evolve continuously, versioning becomes super important and it helps to have a particular convention in place since the inception of the app. App versions should readily convey the degree of changes between app updates. At Gojek, we use [Semantic Versioning](https://semver.org/) and the version name looks like this

> ###### v3.32.0 [MAJOR.MINOR.PATCH]

As the name suggests:

- increment *MAJOR* when you make incompatible API changes or drastic changes — like codebase rewrite or design overhauls.
- increment *MINOR* when you add functionality that’s backward compatible — like introducing a new feature.
- increment *PATCH* when you make hot-fixes that are backward compatible — like fixing a bug in a newly released feature.

## 3. App Rollout

Every app release should be rolled out strategically instead of releasing it to production to all the users in one shot.

> ###### This is where [release life cycle](https://en.wikipedia.org/wiki/Software_release_life_cycle) plays an important role.

Since Gojek is a #SuperApp comprising of a bunch of products that are being built by different teams, we follow something called [Software Release Train](https://en.wikipedia.org/wiki/Software_release_train) to release app updates every two weeks.

We have nightly builds going out every day which are used by devs & QAs.

Once a release candidate is created from the codebase for a particular release, it’s tested by the QAs and we upload it to the App Store only when QAs give a go ahead.

The app is then uploaded to App Store, but is only released to our Alpha Users (who’ll bear with us if something breaks).

Once, the app proves to be stable at this stage, we promote the app to production but proceed with a staged rollout. This involves incrementally increasing the percentage of the update’s availability until it’s 100 %.

## 4. Notifications

> ###### User acquisition is hard. User retention is harder.

User engagement is a crucial part of any product that wants to retain users and achieve/maintain high [DAU/MAU](https://en.wikipedia.org/wiki/Active_users) count. App notifications are a great medium to engage your users and help retain them. But, it’s a double-edged sword.

```
• Send infrequent notifications and the user might uninstall the app due to inactivity.
• Bombard the user with notifications and s/he will get irritated & uninstall the app.
```

This is where analytics play an important role. You can use data to send targeted notifications to a particular cohort. As a user, it’s always pleasant to get personalised/tailored notifications.

Thus, it’s super important to have a good infrastructure to collect user data, divide them into cohorts and run campaigns to keep them engaged.

**Useful Tools: [OneSignal](https://onesignal.com/), [Pusher](https://pusher.com/), etc.**

## 5. Security

I can’t stress this enough, security of your product can make or break it (checkout [DarkNet Diaries](https://darknetdiaries.com/), if you’re not convinced already). Most companies don’t spend much time critically thinking from a security aspect while building their product initially, which proves to be a disaster later on.

Now, as mobile developers we don’t have the mindset of a security engineer which means we don’t think of leveraging a flaw in the product while we’re building it. The best way to compensate for this is to have a dedicated Security team.

At Gojek, we have a dedicated [Security team](https://medium.com/gojekengineering/how-to-secure-a-superapp-304b1c4c5c89) that scrutinises everything before we release anything to production. This is not feasible for indie developers or small startups though, but this doesn’t mean we (mobile devs) should completely ignore this aspect or blame the management for not having a dedicated team.

We can still follow basic security guidelines for mobile app security — obfuscating code, not storing sensitive access keys in the codebase (or anywhere on the device, for that matter). You can take a look at [OWASP Mobile Security Project](https://owasp.org/www-project-mobile-security/) for more information about mobile security.

**Useful Tools: [OWASP Mobile Security Project](https://owasp.org/www-project-mobile-security/), [GuardSquare](https://www.guardsquare.com/), [Android Security Guidelines](https://developer.android.com/privacy-and-security/security-tips), [iOS Security Guidelines](https://developer.apple.com/documentation/security).**

## 6. App Death

Products get discontinued all the time due to various reasons. It’s only reasonable to build an app keeping this in mind, so that if something of this sort happens, the app would be graceful in denying the product offerings once it has been discontinued.

> ###### If you think that this can never happen to your product because it’s super cool — take a look at all of the products Google has [killed](https://gcemetery.co/) till date.

This is something I’ve never implemented myself, neither have I seen it in any of the apps that have been discontinued. But it would be good, to have some sort of error handling at the app side for API failures (with custom error codes) that would notify the user that the *shop is shut and will remain that way forever*.

The only necessity for this pre-emptive implementation is because as app developers, we don’t have the privilege that web developers do wherein they can make changes on the fly and the world would immediately start seeing the Bye, World! page instead of the product homepage (like [this](https://www.orkut.com/) or [this](https://allo.google.com/)).

Okay, that wraps up the important points to keep in mind while building great apps. What do you think about this list? Do you think I missed something? Reach out to have a quick chat about it.

---

*Cross posted on [Gojek Engineering](https://www.gojek.io/blog/six-tips-to-build-great-mobile-apps)*























































