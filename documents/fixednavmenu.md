{{$ meta }}
title: Fixed Nav Menu
{{$ endmeta }}

{{$ layout /layout.html.template as content }}

# The fixed nav menu

To the left you'll notice the navigation menu. 

As you scroll down...

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

It stays put!

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

.

It doesn't always make sense to have a fixed nav menu, however. If you have more items in your menu than can fit on one page, it can be difficult to see the lower items. If you are having trouble with this, you can disable the fixed nav menu by finding this line in `layout.html.template`:

    <div data-spy="affix" data-offset-top="50" class="nav-menu">

And replacing it with this:

    <div class="nav-menu">


