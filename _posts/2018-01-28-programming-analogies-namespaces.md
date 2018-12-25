---
title: 'Programming Analogies #1: Namespaces'
date: "2018-01-28"
tags: ["PHP", "Programming"]
excerpt: If you're unclear on how namespaces work in PHP, maybe this will help.
layout: post
---
This is the first post in a series in which I’ll explain programming concepts by analogy.

Last night I explained what namespaces are to someone, and I thought that the analogy I came up with was very useful and that I’d share it.

Consider this PHP code:

```php
<?php

namespace Music\Genre\Grunge;

class Nirvana {}
```

In this example, the Nirvana class’ full name (or, in nerd-ese, its “fully qualified” name) is Music\Genre\Grunge\Nirvana. As you can probably infer, its *namespace* is Music\Genre\Grunge.

A last name is a good analogy for a namespace. If I know two women named Sara, how can you tell which one I’m referring to? By their last name, that’s how. Sara Skywalker is not Sara Kenobi. The last name serves as the namespace, allowing me to refer to either Sara without ambiguity.

Now, what if you are having a conversation and you’re talking about both Saras? You will probably need to clarify which one you’re talking about each time you mention Sara. You’ll say “Sara S” or “Sara K”, depending. You can do a similar thing with namespaced classes:

```php
<?php

use StarWars\Family\Skywalker\Sara as SaraSkywalker;
use StarWars\Family\Kenobi\Sara as SaraKenobi;

$sara_s = new SaraSkywalker();
$sara_k = new SaraKenobi();
```

This the entire point of namespaces. They are the family names of the PHP world, allowing us to call classes by their nice, short, readable names and not *always* have use their long, fully-qualified, hard-to-read names. Hopefully this analogy will be useful to you. Enjoy!
