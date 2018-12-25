---
title: Optional config voodoo in Drupal 8
date: "2017-11-09"
tags: ["Drupal", "Drupal 8", "Software Engineering", "WTF"]
excerpt: Drupal 8's configuration system is the bees knees, but if you have advanced use cases, you might encounter some WTFs.
layout: post
---
Ah, the config system. Crown jewel of Drupal 8, amirite?

Well, yeah, it’s fantastic and flexible (as is most of Drupal). But if you have advanced use cases — such as building a system that alters config dynamically — there are traps you should know about.

Config is normally a fairly static thing. Your module/theme/profile (“extension” from here on out) has some configuration in its config/install sub-directory, and when the extension is installed, that config is imported. Once it’s imported, that config is owned by your site and you can change it in any way you see fit.

That’s the simplest, and most common, use case in a nutshell. Let’s talk about some other ones.

![The cover of the single version of "The Choice is Yours", by Black Sheep.](/assets/images/black-sheep.jpg)
*Because it's _optional_ config, get it? [Tune.](https://www.youtube.com/watch?v=K9F5xcpjDMU)*

## Optional config
In some extensions’ config directory, you will see an `optional` directory alongside `install`. If you look in there, you see...some YAML files. What’s all this, then?

Optional config is normal config, but it’s treated differently. When an extension is installed, each piece of optional config it provides is analyzed, then imported *only if all of its dependencies are fulfilled*. A piece of config can declare, for example, that it depends on a view called `content`. That’d be expressed thusly in code:

```yaml
dependencies:
  config:
    - views.view.content
```

If that piece of config is optional, then it will only be imported if, well, a view called `content` exists in the system. That view might have been shipped with another extension, or maybe you created it manually. It makes no difference. As long as a view called ‘content’ is present, any optional config that depends on it will be imported as well.

Neat, yes? This comes in quite handy for something like Lightning, which [allows you to create an install profile which “extends” Lightning](https://lightning.acquia.com/blog/extending-lightning-part-ii), yet only installs some of Lightning’s components. Optional config allows us to ship config that might be imported, depending on what parts of Lightning you have chosen to install. Hooray for flexibility!

## Optional config whilst installing Drupal
But wait, there's more.

When you’re doing a full site installation (i.e., `install.php` or `drush site-install`), optional config is treated a little bit differently. In such a case, all extensions are installed as normal, but their optional config is ignored initially. Then, *at the end of the installation process*[1], once all extensions are installed (and their default config has been imported), all[2] the optional config is imported in a single batch.

I don’t think this is documented anywhere, but it can have major ramifications. Consider this piece of code — let’s say it’s part of a module called fubar, which provides some default config and some optional config. This hook will be invoked while fubar is being installed, but *after* its default config has been imported:

```php
<?php

/**
 * Implements hook_install().
 */
function fubar_install() {
  $view = entity_load('view', 'fubar_view');
  $view->setDescription('The force will be with you, always.');
  $view->save();
}
```

`fubar_view` is optional config, so will `entity_load()` return a view entity, or will it return NULL? What do you think?

The maddening answer is *it depends*. It depends on when fubar is being installed. If Drupal is already installed, and you’re just adding fubar to your site, then $view will be a view entity, because the optional config will be imported before `hook_install()` is invoked. But if you’re installing fubar as part of a full site install — as part of an installation profile, say — $view is going to be NULL, because optional config is imported in a single batch at the end of the installation process, long after all the install hooks have run.

![A picture of the Swedish chef muppet, throwing his hands up in the air, with a "Vurt da Furk!" caption.](/assets/images/chef.jpg)

Yeah, it’s a WTF, but it’s a justifiable one: trying to resolve the dependencies of optional config during Drupal’s install process would probably have been a colossal, [NP-complete](https://en.wikipedia.org/wiki/NP-completeness) clusterfuck.

## Dynamically altering optional config
So let’s say you need to make dynamic alterations to optional config. You can’t do it in `hook_install()`, because you can’t be sure that the config will even exist in there. How can you do it?

The easiest thing is not to make assumptions about when the config will be available, but simply react when it is. If the optional config you’re trying to alter is an entity of some sort, then you can simply use entity hooks! Continuing our fubar example, you could add this to `fubar.module`:

```php
<?php

use Drupal\views\ViewEntityInterface;

/**
 * Implements hook_ENTITY_TYPE_presave().
 */
function fubar_view_presave(ViewEntityInterface $view) {
  if ($view->isNew() && $view->id() == 'fubar_view') {
    $view->setDescription('Do, or do not. There is no try.');
  }
}
```

This ensures that `fubar_view` will contain timeless Yoda wisdom, regardless of whether `fubar_view` was imported while installing Drupal. If `fubar_view` is created at the end of the installation process, no problem — the hook will catch it and set the description. On the other hand, if `fubar_view` is installed during `drush pm-enable fubar`, the hook will...catch it and set the description. It works either way. It’s fine to dynamically alter optional config, but don’t assume it will be available in `hook_install()`. Simply react to its life cycle as you would react to any other entity’s. Enjoy!

## Moar things for your brain
* `hook_install()` can never assume the presence of optional config...but it *can* assume the presence of default config (i.e., the stuff in the config/install directories). That is always imported before `hook_install()` is invoked. EDIT: **This is not true during config sync!** See [these comments](https://dev.acquia.com/comment/4341#comment-4341).
* In fact, you can *never* depend on the presence of optional config. That’s why it’s optional: it might exist, and it might not. That’s its nature! Remember this, and code defensively.
* The [config_rewrite](https://drupal.org/project/config_rewrite) module, though useful, can throw a monkey wrench into this. Due to the way it works, it might create config entities, even optional ones, *before* `hook_install()` is invoked. Even during the Drupal installation process. Beware! (They are, however, [working to fix this](https://www.drupal.org/node/2903629).)
* The config system is well-documented. Start [here](https://www.drupal.org/docs/8/configuration-management/managing-your-sites-configuration) and [here](https://www.drupal.org/docs/8/api/configuration-api/configuration-api-overview). This post is only one of a shitzillion other blog posts about config in D8.
* This blog post came about because of [this Lightning issue](https://github.com/acquia/lightning/issues/506). So hats off to Messrs. [mortenson](https://github.com/mortenson) and [balsama](https://github.com/balsama).
* Thanks to [dawehner](https://drupal.org/u/dawehner) for reviewing this post for technical accuracy.
* “NP-complete”, as I understand it, is CompSci-ese for “unbelievably fucking hard to solve”. Linking to the Wikipedia article makes me seem smarter than I really am.

[1] The reason this happens at the end is because any number of things could be changing during installation (not even the Shadow knows what evil lurks in hook_install()), and trying to solve multiple dependency chains while everything is changing around you is like trying to build multiple castles on a swamp. (Only [one person](http://forums.pelicanparts.com/uploads10/HolyGrail1361180669130.jpg) has ever accomplished it.) Don’t think about this shit too much, or it will melt your brain.
[2] “All”, in this case, means “all the optional config with fulfilled dependencies,” not all-all.
