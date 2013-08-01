{{$ meta }}
title: Getting Started
order: 1
{{$ endmeta }}

{{$ layout /layout.html.template as content }}

# Getting started creating documents

To get started creating some documentation, just add a file with the `.md` extension to your `documents` folder. Whenever backlift sees a `.md` file it will automatically convert it into a `.html` file, which it will put in the same folder alongside the original. The compiled `.html` file exists only on the backlift server, you won't see it added to your Dropbox folder.

The content of each `.md` document will be a mix of markdown and Backlift's template tags.

## The meta block

At the top of each page you'll want to add a `meta` block so you can control what's displayed in the nav bar. Following that you should add a `layout` tag to tell Backlift how to layout this new page.

Here's what the top of each new document should look like:

	{{$ raw }}{{$ meta }}
	title: Display This in the Nav
	{{$ endmeta }}

	{{$ layout /layout.html.template as content }}{{$ endraw }}

## The content

The content of each page can be regular [markdown](http://daringfireball.net/projects/markdown/syntax). 

Check out the [markdown examples](/documents/examples.html) section to see a few quick examples of what you can do with markdown.
