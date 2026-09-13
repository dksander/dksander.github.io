---
layout: post
title: "Base App Contribution"
date: 2026-09-13 10:00:00 +0200
categories: bc al
---

Here are my two first experiences with contributing to the BC Base App. If anyone else can get something out of them, great.

## The first one

A while back I got a Base App contribution merged into BC Baseapp. The change was approved and merged, and it fixed an actual bug in the product — but I later discovered that even though it was merged, the fix still wasn't actually in Baseapp for a long time.

Before I complain too much: the whole thing was about a missing `ApplicationArea = All;` on a page object. 
I just wanted to save others the headache going forward ([microsoft/ALAppExtensions#28330](https://github.com/microsoft/ALAppExtensions/issues/28330)). 
Then, many months later, I suddenly noticed it actually *was* in Baseapp. 
I don't know exactly how long it took, but from the outside it was a long process.

For anyone who's curious: this was actually one of the earlier open-source repos Microsoft used to test the process, before they moved Base App over to BCApps. So it was one of the earlier proposals under that setup. In the meantime, Microsoft has since moved everything over to BCApps.

## The second one

Now to my most recent experience, with the whole Base App running on GitHub.

A colleague found a problem in Baseapp, and I thought: hmm, if we're hitting this, others probably are too. Let's try again. I got a GitHub issue written up, a BC idea filed, and approval to get started.

And then... nothing happened for about a week. Until I went in to check *why* nothing was happening, and discovered I'd actually gotten a green light the whole time.

So I fired up my evening and VS Code with all the tools turned up. I wrote code that matched the style of standard BC code, followed all the guidelines, added tests, and so on — I was actually pretty happy with it. I opened a pull request against `github.com/microsoft/Baseapp`.

Then there are a few steps to get through — Copilot review, some license signing — and then, again, nothing happened for a couple of weeks. I started wondering what the actual timeline looked like. Then I got my first review comment, which gave me something to think about, so I let it sit in the back of my mind for a couple of weeks while I found the time and got my thoughts straight.

Then I got the itch to finish it — partly because closing it out would let me kill two birds with one stone for a couple of customers I was working with. So I gave it another go, but this time with Claude Code as the driver.

By that point, it had been weeks since I'd opened the pull request, so I no longer had the environment I'd originally used to develop it, or anything else set up. Claude Code figured out the change was still valid, so I asked it to test it. It found out it could run a full AL-Go build — but since even Claude considered that a long, time-consuming task, I let the code get tested through the CI/CD flow on my own GitHub instead.

Naturally, quite a bit had happened on `main` in the meantime, and Claude actually came up with a sensible git plan:

>
> - Your fork (`xxx:main`) is **478 commits behind** `microsoft:main`, but also 2 commits ahead (your "Add External Document No. field..." commit plus an earlier merge commit).
> - Your open PR ([microsoft/BCApps#10064](https://github.com/microsoft/BCApps/pull/10064)) goes from `xxx:main` → `microsoft:main`. Since the PR uses `main` directly as the branch, GitHub will automatically update the PR once you push new commits to `xxx:main` — you don't need to open a new one.
> - The same file (`CreateBillingDocuments.Codeunit.al`) has been changed several times upstream since your fork was made, so a merge could potentially conflict with your local, uncommitted change.
>
> **The plan I'm suggesting:**
> 1. `git stash -u` — temporarily set your local change aside
> 2. `git merge upstream/main` — pull in the 478 upstream commits into your local `main` (same approach you used last time, per your earlier merge commit)
> 3. `git stash pop` — put your change back on top (resolve any conflict if one comes up)
> 4. Commit your change
> 5. `git push origin main` — this automatically updates PR #10064 with both the upstream sync and your new change
>
> I'd like to test steps 1–3 locally first (reversible, no push yet) to see if any conflicts show up, before we touch anything on GitHub. Should I go ahead with that?

Instead of me having to walk through every step manually, Claude just served it up directly. Smart.

I've now gotten my old pull request restarted, so we'll see over the coming weeks whether anything new happens here.

