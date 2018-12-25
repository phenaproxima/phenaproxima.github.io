---
title: Yet another blog post about dependency injection in Drupal 8
date: "2017-06-03"
tags: ["Dependency Injection", "Drupal 8"]
excerpt: A short Q&A if you're a Drupalist who's heard about dependency injection, but you don't know what that means.
---
If you’re just starting to write Drupal 8 modules, you may have heard whispers about “the container”, “dependency injection”, or even “the dependency injection container” (DIC for short, because programming is more fun with Beavis and Butt-head impressions).

Here‘s a short FAQ about all that. With sloths.

**What is the dependency injection container?**
The dependency injection container is a special object that allows you get services which you can use in your code.

**And what is a service?**
The best definition of a service that I ever heard was given at a DrupalCon Austin session: A service is a useful object. That’s all. Just an object with methods on it that you can call in order to do useful things.

**Is this for that newfangled object-orientated stuff we’re doing in Drupal now?**
Yes. Dependency injection is relatively pointless in procedural code — that is, the style of code you wrote for Drupal 7 and earlier, where most (but not all) things were functions, not objects. Drupal 8 is the opposite — most (but not all) things are objects, not functions.

**So how do I get a service from the container?**
The container has a get() method you can call. You pass it only one argument: the *service ID*. Service IDs are arbitrary strings which are used to identify various services in the container.

**How do I get ahold of the container?**
In a textbook example of a maddening Drupalism, there are actually several different ways to do it (at least in the current version of Drupal). Rather than get lost in that particular jungle, I’ll just tell you the easiest way to get the container:

```php
\Drupal::getContainer()
```

You can also do this:

```php
\Drupal::service($service_id)
```

Which is a shortcut for this:

```php
\Drupal::getContainer()->get($service_id)
```

**How do I find out what services are available?**
Core has a file called core.services.yml, which lists (almost) every service in the container — every service provided by core, that is. Modules can provide services too, by including a MODULE_NAME.services.yml file. These files have the same format as core.services.yml. You can also search for services by service ID on [api.drupal.org](https://api.drupal.org/api/drupal), just like you would search for a function or class.

**There’s like a shitzillion services. How do I know which one I want?**
*Great* question. Perhaps I will write a blog post listing some of the most commonly used services and what they do. But at the moment, it’s pretty much down to trial, error, and skimming through services.yml files. Don’t be shy about asking for help on IRC, Slack, or Twitter.

**What does “dependency injection” mean?**
*Dependency injection* is masturbatory jargon that might impress non-technical folk, but it refers to a super-simple concept: if you have an object that needs to employ the services of some other object — a database wrapper, for example — you should pass that database wrapper to the consumer via its __construct() method, rather than having the consumer deal with setting up the database wrapper itself. (This doesn’t necessarily have to happen in the constructor — the consumer could provide a setDatabase() method or similar for this purpose.)

Here, then, is dependency injection in a nutshell: “How about you just give me the things I need to work with, so I don’t have to bother setting all that shit up myself?” *Inversion of control* is another wanky term you can use to describe this pattern.

**Why should I bother with this fancy-pants dependency injection nonsense?**
I could give you smart-sounding technical reasons like “testability”, “decoupling”, and “encapsulation”, but I’ll go with these instead: all the cool kids are doing it, and I said you should. This is a case where the wisdom of your fellow programmers is, in fact, wise. Just do it. Every time you write a class that uses proper dependency injection, kittens play with sloths and puppies under a gumdrop rainbow. OK?

P.S. Some of the stuff I said in this post is overly simplistic. That’s alright — I’m going for broad, general strokes. There are a bajillion other blog posts that explain this in more detail (though lacking my singular wit ;) Now that you (hopefully) understand the concepts, I hope you’ll go forth and learn more!

P.P.S. \Drupal::service() is not “proper” dependency injection, but I don’t care. Explaining the multiple ways to write DI code in Drupal 8 is way out of scope for this post; the concepts are more important.
