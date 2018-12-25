---
title: I've been using Behat wrong this whole time
date: "2018-08-07"
tags: ["Testing", "Behat", "PHP", "Drupal 8", "Drupal"]
excerpt: My Behat tests drive me fucking crazy, and it's all my fault. Learn when to write Behat tests, and when not to.
layout: post
---
At my day job, I maintain a Drupal distribution called [Lightning](https://lightning.acquia.com). It has lots of tests, and those tests are the bane of my existence.

I can't tell you how many hours I have pissed away just trying to make the tests *stable*. I mean...a test suite is there to tell me if the software works or not, right? And if the tests are constantly failing at random, requiring intricate and bizarre hacks to pass, and generally behaving in an unreliable manner…what's the damned point of having tests?

I'm convinced that Lightning's test suite is single-handedly responsible for the white hairs I'm starting to see on my face. I've wondered how many years it has taken off my life. How much company time has been spent fixing and re-fixing tests which don't themselves work, even though they are testing features which *do*?

And it turns out that all this pain is pretty much *my fault*. Because I have misunderstood my tools.

## Behat BFFs
Most of Lightning's tests are written in [Gherkin syntax](https://docs.cucumber.io/gherkin/) and executed by [Behat](http://behat.org/en/latest/). By itself, Behat/Gherkin is insanely cool because it allows you to write and execute tests that are written in plain English. This means that non-technical people can look at your tests (which are written in English, or whatever your native language may be), see that they're green, and be absolutely confident that the features described are actually being delivered. The sheer *elegance* of it is immensely appealing to a trench-dwelling, allergic-to-clients code monkey like me. Behold this example lifted from Behat's website:

```
Scenario: Buying a single product under £10
    Given there is a "Sith Lord Lightsaber", which costs £5
    When I add the "Sith Lord Lightsaber" to the basket
    Then I should have 1 product in the basket
    And the overall basket price should be £9
```

There are things going on under the hood here, but this post isn't a Behat tutorial, so I'll send you [here](https://behat.org/) if you want to know more about how it works. Suffice it to say that Behat lets you write tests that non-technical folks can understand. They can look at tests like this and be confident that they're *actually getting what they want*. (I can't help but fantasize about how valuable this would have been back in my agency days, and how much aggravating back-and-forth could have been avoided.)

## ...and then things went off the rails
So we've established that Behat is really excellent for writing “stories” which are understandable by non-technical people, and validating them in a concrete way that can be automated. Since learning how to drive it a couple of years ago, I took this truck and drove it directly off a cliff.

![Thelma and Louise driving their car off a cliff at high speed.](/images/cliff.jpg)
*I'm Thelma and Behat is Louise.*

I screwed up by using Behat, and its elegant syntax, as a *testing* tool rather than what it actually is — an *acceptance* tool. Rather than using it **only** to validate Lightning's user stories (i.e., “does Lightning deliver what users want?”), I used it to test virtually everything, including bug fixes, obscure corners of functionality, edge cases, and regressions. Bad move.

We ended up with ugly, complicated tests like this one:

```
@video @javascript @c74eadd0 @with-module:test_c74eadd0
  Scenario: Clearing an image field on a media item
    Given I am logged in as a user with the "create media, update media" permission
    When I visit "/media/add/video"
    And I enter "Foobaz" for "Name"
    And I enter "https://www.youtube.com/watch?v=z9qY4VUZzcY" for "Video URL"
    And I wait for AJAX to finish
    And I attach the file "test.jpg" to "Image"
    And I wait for AJAX to finish
    And I press "Save"
    And I click "Edit"
    And I press "field_image_0_remove_button"
    And I wait for AJAX to finish
    # Ensure that the widget has actually been cleared. This test was written
    # because the AJAX operation would fail due to a 500 error at the server,
    # which would prevent the widget from being cleared.
    Then I should not see a "field_image_0_remove_button" element
```

...and that's one of the *shorter* ones.

Riddle me this: what in the *hell* is a non-technical person supposed to get out of that? What is even being tested? Why would a non-technical person reading this test care at all if an “element” (whatever *that* means) of “field_image_0_remove_button” is there or not? Would they even know what that is?

A non-technical user **doesn't really care** if the correct buttons will appear when they're uploading or removing an image from a media item, or what they will say; **they only want to know that they can upload images into their media library!**

This test doesn't communicate that at all. *Nothing* in this test would mean anything to anyone non-technical. And that's because this is not an acceptance test; it's a *regression* test. And Gherkin is absolutely the wrong tool with which to write regression tests.

## Why can't I write regression tests in Behat?

Well, *technically*, you can. (I did it in Lightning for 2+ years.) You can also technically throw a thousand monkeys into a building, give them two thousand keyboards, and wait for them to crack [prime factorization](https://en.wikipedia.org/wiki/Integer_factorization). But recall the wisdom of Jeff Goldblum in *Jurassic Park*: just because you *can* doesn't mean you *should*, and although you *can* write regression and deep functionality tests in Behat, it is an express train to hell. I know because I (and others) have been there.

![Jeff Goldblum as Dr. Ian Malcolm in Jurassic Park, shirtless and in a come-hither pose.](/images/goldblum.jpg)
*The wisdom of Dr. Ian Malcolm.*

In my view, this is because Gherkin syntax just isn't flexible enough to represent the kind of poking and prodding that's usually needed in regression tests. It tends to “speak” in absolute, context-free statements or commands. But regression tests are often *highly* dependent on state, context, and other fiddly little details that simply don't translate well into short, natural-language sentences. I'm not dissing Gherkin—it's a fantastic tool. But when it comes to testing deep functionality, regressions, or edge cases, it's the *wrong* tool. [Even the creator of Cucumber (upon which Gherkin is based) agrees.](https://cucumber.io/blog/2014/03/03/the-worlds-most-misunderstood-collaboration-tool) In Lightning, I have spent *so much time* writing thousands of lines of code, all in the futile pursuit of twisting Gherkin into being suitable for testing regressions and deep functionality.

Don't make that mistake. **Write Behat tests to prove to your non-technical people that a feature delivers what they want.** If I'd known this when I first started using Behat, the eyesore scenario above would look more like this:

```
Scenario: Adding an image to the media library
  Given I am logged in as a media creator
  When I go to add an image
  And I upload "test.jpg"
  And I enter "Jeff Goldblum" for "Alternative text"
  And I press "Save"
  Then I should see "test.jpg" in the media library
```

This very clearly communicates the feature I'm trying to deliver. Even better, *it documents how to use it*. If I'm also trying to guard against regressions, I can program any one of those steps to do whatever additional work is needed to ensure that the bugs are squashed. But that kind of tomfoolery should never be exposed in the scenario itself.

And if the scenario as a whole doesn't add any non-technical value, it shouldn't be a Behat test. It should be written in a framework like PHPUnit, which is far more suitable for poking around in the dark corners of the software under test.

## The road to recovery
Behat brings value to Lightning not just because of Gherkin, but because it runs its tests against an *existing* site, rather than a completely new one spun up specifically for the test (which is what Drupal's PHPUnit-based test framework does). This allows us to test Lightning's update path, which is largely *not* automatic. I've been looking at [Moshe Weitzman](https://medium.com/@weitzman)'s [new PHP package](https://gitlab.com/weitzman/drupal-test-traits), which allows Drupal PHPUnit tests to run against an existing site, as a way out of the Behat mess I've gotten Lightning into. PHPUnit gives me the flexibility to test my *code*, so I can be sure that everything still works, and Moshe's package lets me combine that flexibility with the assurance that Lightning's complicated update path works.

While that promising package reaches maturity, learn from my experience. If you are using Behat to test things it shouldn't, stop doing it. Write PHPUnit tests instead where you can. If you write Behat tests, let them flow from user stories, and keep the scenarios clear and very well-encapsulated. Encourage this outlook in other developers.

Also, if you've stepped in this bear trap, take heart — it is *all too easy* for us programmers to misunderstand the purpose of Behat, and cause ourselves undue difficulty. I'm sure we can all agree that it's of paramount importance to use the right tool for the right job, so I hope I've helped you understand what Behat is best for, and how you can work *with* it for fun and profit, rather than against it.

P.S. [This talk](https://skillsmatter.com/skillscasts/8633-bdd-is-not-about-testing), by Dan North — who is credited with inventing behavior-driven development (BDD) — hugely helped me understand what Behat, and BDD overall, is actually intended to do. I recommend it. [(Here are the slides from that talk.)](https://speakerdeck.com/tastapod/bdd-is-not-about-testing)

P.P.S. I also want to thank larowlan, benjy, traviscarden, and moshe for straightening out my thinking.
