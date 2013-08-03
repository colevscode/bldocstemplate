{{$ meta }}
title: Template Tags
{{$ endmeta }}

{{$ layout /layout.html.template as content }}

# Backlift template tags

{{$ raw }}
In this kit you'll find a few different Backlift template tags. There's the {{$ meta }} and {{$ layout }} tags, which are described in the [getting started](/documents/gettingstarted.md) guide. Other than that, in the `layout.html.template` file you'll find a {{$ nav }} tag and several {$ variable } tags. Also in this file, we use the {{$ raw }} tag. Here's a quick description of what these tags do. You can find more in the [backlift docs on template tags](http://backlift.com/docs/templatetags).

## {$ variable } tags

Variable tags are used to write the contents of a variable into the html page. Examples of variables include the `meta.title` variable created by a `{{$ meta }}` tag, and the `page` variable created by the `nav` tag.


## The {{$ nav }} tag

The `nav` tag is a block tag which performs an action: it creates a nav menu by listing the files in a specified folder. The `nav` tag has three arguments, a `path`, and CSS classes for the div and link tags that the `nav` tag creates. You can also add filters to the block, which can be used to control the order or filter out results. The CSS classes and filters are optional. 

Here's the syntax:

    {{$ nav <path> [ <list-class> ] [ <item-class> ] [ | <filters> ] }}

*arguments in the [ ] are optional*

This will generate HTML that looks like this:

    <ul class="list-class">
      <li class="item-class"><a href="page-url-1">Page Title 1</a></li>
      <li class="item-class"><a href="page-url-2">Page Title 2</a></li>
      <li class="item-class"><a href="page-url-3">Page Title 3</a></li>
	</ul>

Now let's look at a real example. In the `layout.html.template` file you'll see the nav line that looks like this:

	{{$ nav /**/*.html 'nav nav-pills nav-stacked' | sort {$ page.order } {$ page.title } }}

The first argument (/\*\*/\*.html) is the `path` argument, and it uses wildcards to match the paths of several files. The star matches any html file (\*.html) and the doublestar matches any folder (/\*\*/). 

*NOTE: You may have noticed that there are no .html files in the project folder at all. However, Backlift automatically converts markdown files (.md) into .html files. These .html files are stored on the Backlift server and available to the {{$ nav }} tag.*

The next argument is the list classe, used by Twitter Bootstrap to style the navigation menu. 

Finally the `sort` filter orders the navigation elements, first by the `order` metadata property, then by the `title` metadata property.

You can read more about the {{$ nav }} tag and it's options by checking out the [template tag reference](http://backlift.com/docs/templateref).


## The {{$ raw }} tag

This is just used to keep Backlift from rendering the body of the tag. It's useful for documenting the template tags, but doesn't have any other purpose.

{{$ endraw }}
