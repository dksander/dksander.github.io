---
layout: post
title: "From NuGet feeds to shadow apps – handling 3rd party dependencies in AL-Go"
description: "How we handle 3rd party dependencies in AL-Go for Business Central: NuGet feeds, runtime packages in a dependencies folder, and shadow apps as a last resort."
date: 2026-10-25 07:00:00 +0100
categories: bc al devops
---

This one has been bugging me for a while: 3rd party dependencies in our AL-Go builds.

In this post, I'll go through how we handle them today – from the dream scenario with NuGet feeds to our emergency plan, the shadow apps.

## What really takes time from a developer?

One of my biggest headaches is when a build fails because of a 3rd party extension.

Maybe CI/CD hasn't run since the previous version of BC – and back then it was BC28, not BC29. One of the extensions in the repo's dependencies doesn't support BC29, so boom. 💥

Now it becomes my problem as a developer to get the dependencies in order, even though it has nothing to do with any of the code I've written.

What?! In 2026 this is still an issue? Yes – **BIG TIME!**

As a VAR partner, we often depend on 3rd party apps (mostly from vendors we have a partner agreement with), and sometimes on an AppSource app or another 3rd party we don't know anything about.

## So how do we handle it?

### 1. The NuGet feed

We would prefer to have everything available through a NuGet feed, because it solves most of our problems. Unfortunately, only Microsoft and a few of our vendors have made it there.

I **really** hope more of them follow soon, because for us as a partner, it shouldn't be a blocker for our daily development.

AL-Go supports NuGet feeds ([see the AL-Go documentation](https://github.com/microsoft/AL-Go/blob/main/Scenarios/settings.md#trustedNuGetFeeds)), and it's easy to set up as a fixed value on your GitHub organization – and voilà, you're running.

It's an organization variable called `ALGOORGSETTINGS`:

![The ALGOORGSETTINGS organization variable in the GitHub organization settings](/assets/algo-org-variables.png)

And inside it, the NuGet feed is added under `trustedNuGetFeeds`:

![The value of ALGOORGSETTINGS with trustedNuGetFeeds](/assets/algo-org-settings-nuget.png)

This is the dream scenario, and clearly the one to prefer.

### 2. 3rd party apps with a runtime package

What do we do with the apps that don't have a NuGet feed and don't have a download link?

For those, we've decided to run with a dependencies folder that then lives in the repo ([see the AL-Go documentation](https://github.com/microsoft/AL-Go/blob/main/Scenarios/settings.md#InstallApps)).

That's both good and bad. It works, and it works every time – but it requires a lot of maintenance. We have to make sure we always have versions of these apps that match the current version we are building against. It is quite a hassle.

At the moment I'm debating with myself whether to create a GitHub package that only lives in our organization and put the apps in there, so we only have to maintain them in one place instead of in every repo (I haven't looked into what it takes yet, but it's a thought). The downside is that updates would then depend on just a couple of people, which could become a bottleneck and block the other developers.

### 3. 3rd parties that won't give us a runtime package (or anything else)

We still need to be able to run a build and execute tests.

So we started using what we call **"shadow apps"**.

**What is a shadow app?**

An app that makes AL-Go believe we have the real app, without us actually having it. It can live in the dependencies folder in Git, so the build can find the dependency.

**A shadow app is ONLY used in case of emergency**

A shadow app is a last resort, not the first choice. The order is:

1. Get the app or a NuGet feed from the vendor.
2. Ask the vendor for a runtime package.
3. Only then – make a shadow app.

**How do you make a shadow app?**

1. Create an **empty AL project**.
2. Copy the information about the 3rd party app into `app.json`: **App ID, Publisher, Version and Name**. You can find them under dependencies in your own app's `app.json`.
3. Put the app in the dependencies folder in Git.
4. Create **empty objects** that expose what the real extension contains, and that your extension uses. For example a codeunit with the functions you call, or a table or some fields from a table.

In other words: an empty shell that matches the real app's "interface" just enough for your code to compile.

Naturally, tests based on a shadow app are not valid, and they should be avoided. The shadow app was only invented out of necessity.

## Wrapping up

Shadow apps get the build running again, but they are a workaround, not a solution. The real fix is in the hands of the vendors.

So, dear ISVs: if you build apps that partners depend on, please give us a NuGet feed 🙏 It saves us a lot of time, it probably saves you a few support questions about versions too, and it lets us spend our time on the code that actually matters to our customers.

Until then, we'll keep maintaining our dependencies folders – and keep the shadow apps for emergencies only. 😉
