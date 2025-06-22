---
title: "A Guide to Tackling Huge Codebases"
date: 2019-01-29T23:29:21+05:30
draft: false
author: "Cyril Pillai"
tags:
  - Codebases
  - Android
  - Kotlin
image: /images/blogs/huge_codebases/cover.webp
description: ""
toc:
---

I recently joined GOJEK as a Product Engineer as part of the GO-LOGISTICS team. I’ve been doing Android Development for over four years, but when I first ‘git pulled’ **GoHost** (the monorepo for the Android consumer app), I was intimidated by the codebase. I have worked with codebases that had four to five contributors, but this was a different beast altogether.

80+ modules, 20+ active contributors, ~20 mins for a clean build, and different coding styles/architectures in different modules. Product modules are owned by the respective teams and thus they have the liberty to choose what works best for them.

> On an unrelated note, codebases are the stuff of folklore at GOJEK. When we launched our app in 2015, all our backend services depended on a legacy codebase named after a South Park character, Stan Marsh. As GOJEK grew, Stan Marsh buckled under the strain. We eventually pulled all its functionality into discrete microservices. Nevertheless, the legacy of Stan Marsh lives on.

I was lucky enough to have access to some of the developers who oversaw the transition from Stan Marsh, and gained a lot of insights. This post pens down my learnings from the last few months on how to deal with huge codebases.

## You can’t understand everything

<p align="center">
    <img src="/images/blogs/huge_codebases/know_nothing.webp">
</p>

> ##### This is the hardest thing of all.

All of the previous codebases I’ve worked on/owned had one thing in common — I knew everything, about everything. The legacy ones I inherited took some time and effort but eventually I used to get well-versed with the entire system.

**I tried that approach here and failed miserably**. Initially, I was responsible for a single module (GO-SEND product) out of the 80+ modules. These modules included other products (GO-RIDE, GO-FOOD, GO-TIX etc.), libraries/utilities, SDKs for the products, and product agnostic core/platform code.

After a few days of diving in the ocean of code and sinking every time, I got frustrated and talked to my Mobile Lead about this.

> ##### Talking to him made me realise that no one in the entire org knows everything about the system.

The key takeaway here is to understand the parts you own or would work on, and be oblivious to other moving parts. Changes are being made to the codebase on an hourly basis, and it’s humanly impossible to keep track of everything.

## Get familiar with the architecture of the codebase

Now, when I say be ***oblivious***, I don’t mean everything. It’s a mandatory process to dive into the ocean and sink, but you need to know when to stop. Try to familiarise yourself with the skeleton of the codebase. **Read code from modules that make up the core of the system (platform code that is independent of the products), and understand how your module interacts with that code**. Dig into SDKs and internal libraries that abstract certain third party dependencies, so that the product modules aren’t affected by future changes.

## Have a specific goal in mind before you dive

It’s important to have a reason to dive into the ocean, and to have a well-defined task to finish and get out (because time ticks 🕒). If you don’t have any task, ask your team members to assign you something. It’s better to get up from the chair feeling good about completing something. Definitely beats getting lost in the gigantic world of programming constructs.

## Read code thoroughly, don’t break anything

Let’s acknowledge that we developers can be a bit narcissistic about code. We love writing code but reading it bores us to death. Even if we trick our minds into reading someone else’s code, there is usually an urge to rewrite it. The best thing to do here is to be objective and follow one of GOJEK’s [guiding principles](https://medium.com/gojekengineering/go-jek-engineering-guiding-principles-101-9a7bd499ffa2):

> ##### ***“Every decision is correct at the time it is made”***

Spend ample time reading the code and critically evaluate the consequences your changes might have before proceeding.

## Follow the coding style of the file you are editing

This is to ensure that your changes don’t look out of place, and are aligned with the surrounding environment. It’s super important to maintain consistency no matter how outdated something feels.

Assume you need to change the filtering condition in a file:

> ##### What!! This class is still using RxJava 1.0. 😱

*Option 1*

```
• I'll just update the filter condition as per the new requirement.
```

*Option 2*

```
• But, RxJava 2.0 has been around for such a long time.
• Let me upgrade it to the latest version.
after 3 hours
• I've modified a bunch of files and there's still a lot more to refactor.
git reverts everything
• GOTO Option 1.
```

## Talk to other devs

> ##### Rockstar developer / coding ninja / 10Xer / one man army / everyone is inferior to me / I like working alone / others are stupid.

If you relate to anyone of the above things, you need to change. Can you build systems single handedly? Yes. But to make scalable solutions and improve yourself, **it’s important to throw your ego out of the window**. Be liberal and collaborate with others.

Coincidentally, this also helps in dealing with huge codebases. Talk to other developers who have worked on the piece of code you’re concerned with. To get more context, ask about the rationale behind the decisions made and the implications new changes would have. Be open to suggestions on the way you should approach the problem.

## Improve it

— Make the codebase a bit stronger than it previously was by making sure that changes you make are supported by tests.

— Refactor wherever ***NECESSARY***.

— Document decisions/assumptions/hacks to make sure the information is present somewhere (apart from the head of an engineer).

---

*Cross posted on [Gojek Engineering](https://www.gojek.io/blog/a-guide-to-tackling-huge-codebases)*














