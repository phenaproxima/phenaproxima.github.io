---
title: "Lightning's Oxford Comma Helper"
date: "2017-05-03"
tags: ["Drupal 8", "Grammar", "Pedantry"]
excerpt: A useful function I added to Lightning. I hope it helps you.
---
I added a useful function to the [Lightning distribution](https://drupal.org/project/lightning) of Drupal 8! Its purpose is to apply the Oxford comma (with the final conjunction) to an array of strings, returning a single nicely formatted string.

I did this because I don’t enjoy seeing this kind of thing littered throughout Drupal:

> The following file types are accepted: gif png jpg txt pdf doc

It gets the point across, and clearly, but it feels so...*careless* to me. A little polish wouldn’t hurt. Now, if you’re using Lightning, you can generate the string like so:

```php
use Drupal\lightning_core\Element;

echo 'The following file types are accepted: ' . Element::oxford(['gif', 'png', 'jpg', 'txt', 'pdf', 'doc']);
```

This will produce “The following file types are accepted: gif, png, jpg, txt, pdf, and doc”. Much nicer! In fact, this opens the door to smoother phrasing in general:

```php
Element::oxford(['gif', 'png', 'jpg']) . ' files are acceptable.';
```

This will produce the even nicer “gif, jpg, and png files are acceptable.” Yowza!

Element::oxford() will default to using ‘and’ as the final conjunction, but you can override that:

```php
Element::oxford(['apples', 'oranges', 'mangoes'], 'or');
```

This will produce “apples, oranges, or mangoes”.

The function is smart enough to *not* apply the Oxford comma if there are fewer than three items. So this:

```php
Element::oxford(['foo', 'bar']);
```

...will produce “foo and bar”.

I think you get the idea. Go forth, Lightning users, and generate smoother, more grammatically correct lists of items!
