---
title: How to bend Drupal 8 plugins to your nefarious will
date: "2017-05-03"
tags: ["Drupal 8"]
excerpt: In Drupal 8, plugins are everywhere. Here's how you can take full control of them.
layout: post
---
So, Drupal 8 plugins are pretty much the best thing ever. They make huge parts of Drupal swappable and improve its extensibility by orders of magnitude. The plugin system is very flexible, but I want to show you a neat trick that will, in most cases, allow you to bend *other peoples’* plugins to your will.

## Things you need to know about plugins
If you don’t already know the basics of the Drupal 8 plugin system, [start with the excellent drupal.org documentation](https://www.drupal.org/docs/8/api/plugin-api/plugin-api-overview).

Plugins come in many different flavors. But they all, without exception, have three things in common:

1. **All plugins are identified by a plugin ID**, which is an arbitrary string specified by the plugin author as part of the plugin definition.
2. **All plugins are associated with a specific class.** This plugin class is part of the plugin definition, but it’s usually inferred automatically by the plugin system during the discovery process (i.e., when Drupal searches your code base for plugins of a particular type).
3. **All plugin types have a plugin manager**, which is responsible for discovering which plugins of that type are available to Drupal, and for creating instances of specific plugins when you want them. The plugin manager is just a normal service defined in a *.services.yml file.

Now it gets interesting. Most plugin managers (though not all) will, once they’ve scanned the system and found a set of plugin definitions, invoke an alter hook that allows you to alter those definitions! In most cases, you can change any part of any plugin definition this way — including which class a particular plugin should use. That’s incredibly powerful because it allows you to override the *implementation* of a particular plugin, rather than provide a whole new, but ultimately separate plugin that extends the one you want to alter.

## An outlandish example
First, a disclaimer: tinkering with secure text filters is a **terrible fucking idea**, and so is the <blink> tag. Never do this on any server that’s not your sandboxed, locked-down localhost.

Now, then: in Drupal 8, text filters are plugins. [Several are provided by the Filter module out of the box](http://cgit.drupalcode.org/drupal/tree/core/modules/filter/src/Plugin/Filter), which you can peruse at core/modules/filter/src/Plugin/Filter. In this example, we’ll mess with the one called FilterHtmlEscape — a text filter which escapes all HTML tags for output. Let’s say that we are weird, naughty bastards who don’t care about security or good taste, and we want this filter to escape all HTML except for the venerable <blink> tag.

We *could* implement a totally new plugin which does this, but let’s say we want this behavior to take effect retroactively. We really, truly want all text that uses FilterHtmlEscape to let <blink> through.

First, let’s write our special plugin. It’ll inherit from FilterHtmlEscape, but make a small exception for <blink>:

```php
<?php

namespace Drupal\mymodule\Plugin\Filter;

use Drupal\filter\Plugin\Filter\FilterHtmlEscape;

class InescapableBlink extends FilterHtmlEscape {
  /**
   * {@inheritdoc}
   */
  public function getHTMLRestrictions() {
    $restrictions = parent::getHTMLRestrictions();
    $restrictions['allowed'][] = 'blink';
    return $restrictions;
  }
}
```

Cool. So how do we get the filter system to use this class for escaping HTML, rather than FilterHtmlEscape?

The first thing we need to know is the name of the plugin manager’s alter hook, which will allow us to change the plugin definitions. This is usually undocumented, so you need to look in the plugin manager itself to find out. 99% of the time, if the plugin manager supports the alter hook at all, it will be mentioned in the plugin manager’s constructor. In this case, the plugin manager is Drupal\filter\FilterPluginManager, and its constructor looks like this:

```php
public function __construct(\Traversable $namespaces, CacheBackendInterface $cache_backend, ModuleHandlerInterface $module_handler) {
  parent::__construct('Plugin/Filter', $namespaces, $module_handler, 'Drupal\filter\Plugin\FilterInterface', 'Drupal\filter\Annotation\Filter');
  $this->alterInfo('filter_info');
  $this->setCacheBackend($cache_backend, 'filter_plugins');
}
```

This line is what we’re looking for:

```php
$this->alterInfo('filter_info');
```

This means, once it collects all filter plugin definitions, the plugin manager will invoke hook_filter_info_alter() and pass it an array of plugin definitions (which will **almost** always be arrays themselves), keyed by plugin ID, for us to play with. So we’ll implement that hook in our module:

```php
function mymodule_filter_info_alter(array &$definitions) {
  // Time to get schwifty in here
}
```

Now we need know which plugin we’re trying to hijack. $definitions will be keyed by plugin ID, so let’s look at FilterHtmlEscape’s annotation to find its plugin ID:

```php
@Filter(
  id = "filter_html_escape",
  title = @Translation("Display any HTML as plain text"),
  type = Drupal\filter\Plugin\FilterInterface::TYPE_HTML_RESTRICTOR,
  weight = -10
)
```

Boo-yah. The plugin ID is filter_html_escape.

So all we need to do in our alter hook is specify which class to use for that plugin:

```php
function mymodule_filter_info_alter(array &$definitions) {
  $definitions['filter_html_escape']['class'] = 'Drupal\mymodule\Plugin\Filter\InescapableBlink';
}
```

Clear all caches to rebuild the plugin definitions, and boom — you’ll have <blink> tags all over the place, assuming your site’s authors are as crazy as you :)

And now you know everything you need to know about plugin hijacking. Use this power for good. Enjoy!

## P.S.
Q: How did you know that the plugin definition would contain a ‘class’ key which specifies the plugin class?

A: Because that information is automatically added to every plugin definition by the plugin system. It *needs* to know that in order to instantiate the plugin when needed. So plugin definitions will *always* specify an instance class. Change that, and the plugin becomes yours.

## P.P.S.
I am *not* a dark wizard. You can seriously fuck shit up with this technique. Use it wisely and well.
