{{$ meta }}
title: Welcome
order: 0
{{$ endmeta }}

{{$ layout /layout.html.template as content }}

# Welcome to the docs kit!

This is a Backlift kit for building documentation websites. It uses [Twitter Bootstrap 3.0](http://getbootstrap.com/) for styling. 

With this kit you can:

- Write all your docs using simple [markdown](http://daringfireball.net/projects/markdown/syntax).
- Easily add to or update your docs by saving files in the website's `documents` folder.
- Define the layout of your documentation in one place, the `layout.html.template` file.
- Control the text that's displayed in the navigation menu, and the &lt;title&gt; tag by editing the {{$ raw }}{{$ meta }}{{$ endraw }} block at the top of each markdown document.

You don't have to manually update the navigation bar. Instead this kit uses backlift's {{$ raw }}{{$ nav }}{{$ endraw }} tag to automatically detect and add all your documents to the navigation bar.
