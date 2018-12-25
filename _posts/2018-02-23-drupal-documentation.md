---
title: How can we fix Drupal's documentation?
date: "2018-02-23"
tags: ["Drupal", "Documentation", "Community", "Open Source"]
excerpt: Drupal's documentation is subpar, so I offered some unsolicited opinions about how we might improve it.
layout: post
---
I wasn’t going to do Drupal stuff tonight, but [this excellent blog post](http://matthewgrasmick.com/node/126) by a colleague of mine lit a fire under my ass.

Drupal’s learning curve for newcomers is unbelievably steep. As grasmash points out in his blog post, documentation is one of Drupal’s biggest problems. It’s not a complete loss, but it leaves a lot to be desired. There is plenty of useful information, but it’s not very well organized. This isn’t just a *little* unhelpful — it’s a significant obstacle to getting started with Drupal! grasmash rightfully sounds the alarm, because literally every other major framework I know of has *fabulous* documentation compared to Drupal. How can we fix this?

Well, Dries, if you’re listening: I have a thought. I think the Drupal community should undertake a first-class, long-term Documentation Initiative. I don’t know if this has been tried before, but either way, it is *absolutely* worthy of becoming a full-fledged core initiative, with all the resources and support that existing initiatives enjoy.

As I do, I’d like to share some random, unorganized thoughts about what a Drupal Documentation Initiative might look like. I envision three distinct phases (bear in mind that these are just ideas I’m throwing around):

## Phase I: Standards & Governance
* Official, published Drupal documentation should not be editable by any random person with a drupal.org account.
* There should be a new, official role in Drupal’s governance model: that of Documentation Maintainer. These people would have similar power and stature as committers currently do, and they would be mentioned in MAINTAINERS.txt. However, their job would be to review and publish changes to Drupal’s official documentation, rather than control changes to the code base. Ideally, they’d be a mixture of experienced site builders and developers, so that the documentation can properly address Drupal’s mixed audience.
* Commits to core should be blocked on documentation. If you are going to add a feature to core, you should have documentation for that feature which will go live as soon as your patch lands. Documentation maintainers would have the power to block commits until the appropriate documentation was written up to the agreed-upon standards. In other words...**treat documentation exactly how we currently treat patches**.
* Unpublished, in-progress documentation could be worked on by anyone. Once a documentation change goes live, everyone who worked on that set of changes would receive an honest-to-Gawd commit credit for it. Documentation is hard, crucial work and should be recognized accordingly. As a legendary core developer once told me, credit where credit is due is the foundation of open source, and there are *many* contributors who could do invaluable, top-notch work in the documentation space if it wasn’t thankless, second-tier drudgery.
* Where appropriate, documentation should use images and video to illustrate concepts. Documentation maintainers should be able to kick proposed changes back for lacking enough audiovisual material.
* Documentation should have plenty of examples.
* Proposed documentation changes should always be vetted and tested by subsystem maintainers to ensure technical and practical accuracy. It should also be vetted by documentation maintainers to ensure that it is well-written, well-organized, concise, and properly proofread.
* It should be *real* easy to format documentation in a consistent manner. To me, that means Markdown support. Making authors mark everything up in pure HTML is asking for trouble, and will slow down any effort to get good documentation written.

## Phase II: Overhaul & Migrate
There is plenty of good documentation already on drupal.org, and that fine work should not be thrown out. Once official documentation standards exist, and the rules for governing it are established, we should put all of that existing documentation under the microscope. Edit it, reorganize it, massage it, trim the fat, polish its shoes, put a bowtie on it, and just generally *fix it up*. Make it feel real solid and official. Pepper it with images and examples. Then post it on drupal.org, removing edit access from regular users. And begin applying the agreed-upon standards and governance going forward.

## Phase III: Implementation
I believe we should open a new subdomain of drupal.org (docs.drupal.org, perhaps), which will host the official, canonical Drupal documentation. Spinning it up as a subdomain would give us the latitude to implement the documentation governance model and standards in code, taking advantage of the myriad content authoring improvements in Drupal 8 (such as Content Moderation, Workflows, and — eventually — Workspaces, to name a few).

A dedicated subdomain would also give us the latitude to design and implement a dedicated theme, with an an attractive typographic and style guide, that will make our kick-ass documentation a pleasure to read (few things are more unbearable than walls of ugly text with too little space between lines). The current documentation isn’t hideous by a long shot, but I have to assume that it is, at least to some extent, shackled to the styling and design in use elsewhere on drupal.org.

## In closing...
Documentation is Drupal’s pink elephant in the room. It’s not mind-bendingly *horrible* now — the useful information is there! But it’s hard to find, and hard to apply. We’ve been quite good recently about taking cues from our competition and making improvements as rapidly as possible. We did it for core, and we can do it for our documentation too. This can only help us.

What do you think?
