---
title: My very angry wish list for drupal.org
date: "2017-05-10"
tags: ["Drupal", "Rant"]
excerpt: Happy early Festivus! I air my impotent grievances about the state of drupal.org.
---
**Disclaimer:** This is a rant about drupal.org, which is used and loved by thousands of people. I like to write forcefully, but [I’m willing to put my money where my mouth is](https://www.drupal.org/node/2877439) and fix these things. Don’t hate me.

---
I’m a busy bee: I’m involved in like, three Drupal 8 core initiatives. I fix a lot of bugs. I maintain a distribution. I spend an *awful* lot of time on drupal.org. And boy...it has got to be **one of the most frustrating websites in the history of the world**.

Now let me first say that this is not the least bit personal. I have nothing but the utmost respect for the people who work on drupal.org, and have worked on it. They face enormous challenges that I don’t, which they handle with aplomb. They are smart and kind and deeply capable. And they are making excellent progress — the upcoming adoption of Gitlab fills me with joy and child-like excitement. I love being in this community of insanely bright people so committed to progress, even if that progress is messier and/or slower than we’d like.

But I also love to vent my nerd rage, and with that in mind, here is my ignorant pissant’s 10-item wish list for drupal.org, specifically the issue queue. Several of these are niceties that *couldn’t* be too hard to implement. Although I admit I’m ignorant of the day-to-day realities of running drupal.org, and I haven’t looked at the code — which is written on Drupal 7 — I have [requested a dev sandbox in which to try to fix some of these things](https://www.drupal.org/node/2877439).

So without further ado, right off the top of my head and in no particular order:

1. We need to stop anchoring issue comments by their unpredictable internal serial IDs. The comments themselves are sequentially numbered in a way that makes sense — you know you’re about to post comment #32. But if you want to link that comment from another issue, you have to scroll through the issue and find your comment and right-click to get the link to the anchor, rather than just be able to link directly to #comment-32. **EDIT:** I guess we don’t need this, seeing as how it’s already implemented! See below.
2. Make it possible to have an inline reference to a specific comment in a specific issue! We can already reference issues with [#12345678] syntax, but if you need an elegant way to reference a specific comment, you’re shit outta luck. We should be able to do things like [#1234567:89]. Or perhaps [:45] to reference a comment in the same issue. **EDIT:** Turns out this is already possible using [#1234567–89] syntax! I’m so glad I used the words “ignorant pissant” to describe myself before. Coverin’ ass like a champ!
3. I *loathe* the typography on drupal.org, especially the issue queues. It’s small and hard to read, there is no line spacing, and the formatting of simple markup looks rather slapdash to me. Considering that digging into any issue means slogging through reams of complex and picky information, drupal.org issues should be a **pleasure** to read. I bet many expert designers would jump at the chance to fix these problems. GitHub does an *infinitely* better job of it, and there’s no reason we couldn’t too.
4. Markdown support. Why don’t we have this yet? I swear, [there is a module for that](https://www.drupal.org/project/markdown). Some folks hate Markdown, so OK: implement it anyway as an opt-in thing that individual users can enable for themselves! I *hate* having to type raw HTML just to make a coherent comment or issue summary.
5. If an issue is postponed on other issues, it should be automatically reactivated (Active if there’s no patch, Needs Review if there is) when all of the blockers are fixed. Surely this is something that could be done in an hour with an entity reference field and a cron hook.
6. On the heels of that, automatically close meta-issues when all of their children are fixed or closed. In fact, start treating meta-issues (or any non-patch issue) as separate first-class citizens — a whole new type of issue with specialized fields.
7. When working in a very long issue (50+ comments) it is downright uncivilized for me to have to scroll all the way up from a comment in order to look at something in the issue summary. Every single comment should have a “top” link which kicks you right back to the issue summary. I find it hard to believe that this would be very tricky.
8. I hate seeing empty “view issue summary changes” comments cluttering up long issues. There should be a separate tab for reviewing issue summary changes. They should not be tied to comments. I should be able to just directly edit the freakin’ issue. (The [Diff module](http://drupal.org/project/diff) might be useful there.)
9. The “related issues” field is too vague to be useful for big issues, or issues with many blockers or follow-ups. Split it into several fields — “blockers”, “follow-ups”, “main meta-issue”, etc.
10. Ape GitHub again and create a way to reference commits inline. Something like {drupal/a3c257fbe} would be a really nice way to reference commit a3c257fbe in core.

What do you think? Did I miss anything? Am I way out of line? Have all of these been languishing for years in drupal.org’s own issue queue? Is it all going to be solved by Gitlab? Have at me.
