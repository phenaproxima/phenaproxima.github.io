---
title: 'Drupal 8 Media: full st(r)eam ahead'
date: "2017-05-19"
tags: ["Media", "Drupal 8"]
exceprpt: A Media module officially lands in Drupal core. Now the real work begins...
---
![The unofficial logo of the Drupal 8 Media Initiative.](/images/media-initiative.png)
*Courtesy of [Sam Mortenson](https://www.drupal.org/u/samuel.mortenson).*

This morning, [this patch](https://www.drupal.org/node/2831274) (don’t click the link, it is an incredibly long issue that will slow your browser down to a crawl) was committed to the 8.4.x branch of Drupal core. And _**I am so damn happy**_, I have not yet returned from orbit.

With this commit, the Drupal 8 Media initiative picks up steam as we hurtle towards our deadline (yes, we have one) to get a stable Media API into core. I want to explain what’s going on here, and possibly how you can help. There is [a roadmap issue](https://www.drupal.org/node/2825215), but it’s almost ungrokable unless you’ve been part of the Media initiative since it began. This post is for those who haven’t.

So, okay: what’s going on here?

Well, there is a new module in core. It’s called Media. It’s a refactored port of the [contributed Media Entity module](https://www.drupal.org/project/media_entity), and its purpose is to provide a basic API for dealing with all kinds of media (files, images, videos, tweets, instagrams, more exotic things…etc.)

Media was committed as **beta experimental**, which you can take to mean “we expect it to be stable by a certain date”. The goal is to make it non-experimental — i.e., stable — by Drupal 8.4. This is a relatively tight deadline, but I think we can do it. We have a plan!

One of Media’s goals is to ultimately replace the use of file and image fields for general-purpose media handling. The file and image fields we know and love will still exist, but the hope is that when you want to, say, add an image to a content type, you will add a media field instead of an image field. The advantage of this is that **you will be able to re-use files or images you have previously uploaded**. (Or, because the definition of “media” is so broad, it could be a video, tweet, instagram, or whatever.)

The ability to add such fields, which will interact with the media system under the hood but (for now) behave like normal file and image fields, is the main thing we *must* finish by the time Drupal 8.4.0-alpha1 is tagged.

This takes the form of several core issues, which must be completed in a particular order. All of them were blocked on the big, scary Media patch — but now that’s been committed, and a very clear to-do list has emerged in its wake:

1. **We must implement support for treating files as media items.** This will involve creating a new “media source” plugin (these plugins are the main API provided by Media). There is a patch, and the issue is proceeding: https://www.drupal.org/node/2831936.
2. **We must implement support for treating _images_ as media items.** This work will directly extend the work done in the previous issue. Again, it’s quite self-contained — all we need is a new media source plugin that is specifically for images. **This is blocked until there is support for files**, but the issue is [https://www.drupal.org/node/2831937](https://www.drupal.org/node/2831937).
3. We will then need to **provide specialized field widgets for interacting with media fields**. These widgets will look and behave like regular file and image widgets, but rather than create and reference uploaded files, they will reference media items instead. It’s a neat bit of jiu-jitsu that will be transparent to users. The tricky part is ensuring that they behave as much like standard file and image fields as possible. There will be two new widgets — one for media items that are images, and one for media items that are generic files. Images must be supported first. These issues are located at [https://www.drupal.org/node/2831941](https://www.drupal.org/node/2831941) (images) and [https://www.drupal.org/node/2831940](https://www.drupal.org/node/2831940) (files), respectively.
4. Finally, **we will need to provide specialized field formatters that can display media fields**. As with the widgets, these will look and behave like regular file and image formatters, but they will actually render media items. Again: it’s jiu-jitsu that must be transparent to users. And again, there will be at least two formatters, one for images and one for files, with images getting done first. Currently, there is only one issue for these — [https://www.drupal.org/node/2831943](https://www.drupal.org/node/2831943) — but it will probably be split into two before the work begins.

Once these things are done, in that order, the Media module will be declared **stable** (although hidden), and contrib modules will be able to use its API without fear!

Obviously there is a lot more going on in the Media initiative, and the roadmap is substantially longer. But as I said, these are the main things that we **must** have in Drupal 8.4.0-alpha1, or things will get dicey for us.

The good news is, they’re all relatively easy (at least when compared to the patchzilla that contained the main Media module)— but they won’t get done without the help from the community. So if you have the skills and the inclination, **jump in and join us!** We’ll help you — the Media team hangs out on IRC in #drupal-media, and on the Drupal Slack in #media.

See you in the queues!
