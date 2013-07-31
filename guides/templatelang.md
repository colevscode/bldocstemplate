{{$meta}}
type: guide
title: The Template Language
{{$endmeta}}

{{$layout /index.html as content}}

## Overview

Backlift websites can take advantage of backlift's powerful templating language. This language was designed from the ground up to help non-programmers easily build modern websites. When someone on the internet accesses one of your Backlift HTML pages, the template tags on the page are converted into plain HTML. This is called "rendering" the page, and allows you to add dynamic data to your webpages. For example, you can search through all the images in a "photos" folder and build a gallery on-the-fly.

Here are just a few of the things you can do with Backlift's template language:


### Include commonly used snippets of HTML into your files:

This way you can avoid repeating yourself and keep your code organized.

/blog.html:

    {{$ raw }}{{$ include /nav_snippet.html }}
    <h1> Welcome to my blog! </h1>{{$ endraw }}

/nav_snippet.html:

    <ul class="nav">
      <li><a href="/home.html">home</a></li>
      <li><a href="/blog.html">blog</a></li>
    </ul>


### Put common boilerplate code for all your webpages into a base layout:
      
/blog.html:

    {{$ raw }}{{$ layout /layout.html as content }}
    <h1>My blog!</h1>{{$ endraw }}

/layout.html:

    {{$ raw }}<html>
      <head>
        <title>My website</title>
      </head>
      <body>    
        {$ content }
      </body>
    </html>{{$ endraw }}


### Get data from an API and insert it into your template:

    {{$ raw }}{{$ with /backlift/data/settings/blogsettings as settings }}
      <h1>Site settings:</h1>
      {$ settings.blabla }
    {{$ endwith }}{{$ endraw }}


### Show a list of files from a folder in your project (using the Backlift files API):

    <ul>
      {{$ raw }}{{$ each /backlift/files/*.html as file }}
        <li>Filename: {$ file.filename }, created on: {$ file.created }
      {{$ endwith }}{{$ endraw }}
    </ul>


## Why a new template language?

We designed Backlift's template language to fulfill all the following requirements:

1. Have a simple syntax that's easy for non-programmers to understand.

2. Render quickly without requiring preprocessing or a compile phase.

3. Work safely with user submitted content. In other words it can't allow arbitrary code execution.

4. Allow branching and iteration. In other words, it's not "logic-less". 

5. Work with dynamic data, not just static files. It natively speaks JSON APIs.

Above all, it's tailored specifically for building websites without requiring a backend framework like Rails or Django.


## General syntax

Backlift templates contain two different kinds of tags, block tags and variable tags.

### Block tags:

Use block tags when you want Backlift to perform some action and then display the results of that action. For example, you can include the content of another file into you HTML with the include tag, or you can loop through a list and write out each element with the each tag. Here are some examples of block tags:

    {{$raw}}{{$ include /path/to/somefile }}

    {{$ each /backlift/data/somelist as element }}
       {$ element.name }
    {{$ endeach }}{{$endraw}}

The syntax for all block tags looks like this:

    {{$raw}}{{$ <tagname> [<optional tag arguments> ...] }}
    [ <optional body> 
    {{$ end<tagname> }} ]{{$endraw}}

### Variable tags:

Use variable tags to write out the contents of a variable. Variables generally look like this: `{$ variable }`. Variables are created by certain blocks, and can be used inside the body of the block. For example:

    {{$raw}}{{$ with /backlift/data/somedata as data }}
      {$ data }
    {{$ endwith }}{{$endraw}}

In the above block, the data available from the url `/backlift/data/somedata` is assigned to the variable `data` and is available within the block by using the variable tag `{$ data }`.

Variables can also be referred to within a block tag. For example, you can use the `if` tag to check if a particular variable exists like this:

    {{$raw}}{{$ if {$ data } }}The data variable exists!{{$ endif }}{{$endraw}}

### Filters:

Filters can be applied to the results of block tags. Filters are used to modify the results of the block before it's written to the page. Filters are separated from the tag name and arguments with a `|`. For example:

    {{$raw}}{{ each /backlift/data/files/*.html as file | sort {$ file.created } }}
      {$ file.filename }
    {{$ endeach }}{{$endraw}}

The above block includes the `sort` filter, so it will write out the filenames of all *.html files in the order they were created.

You can append multiple filters by separating them with `|` characters. 

## Automatic variables

All html pages on Backlift have access to some automatic variables added by Backlift. When someone on the internet accesses one of your Backlift HTML pages, the page is converted from template tags into plain HTML (it's "rendered") using a bunch of data to fill in those variables (the "context"). When you're creating your HTMl file, you can access the context by using the variable syntax above. For example:

    {{$raw}}<h1>{$ meta.filename }</h1>{{$endraw}}

Here is a list of the data that Backlift automatically adds to the context of an HTML page before rendering:

- The **meta** variable: This contains data about the document, or "metadata." Examples of metadata include the time the file was created, and the file's path. Here is a list of all the meta properties:

        {{$raw}}{$ meta.created }{{$endraw}}  - The date/time that the file was created
        {{$raw}}{$ meta.modified }{{$endraw}} - The date/time that the file was modified
        {{$raw}}{$ meta.filename }{{$endraw}} - The name of the file, without it's path (like "index.html")
        {{$raw}}{$ meta.fileid }{{$endraw}}   - A unique ID for the file
        {{$raw}}{$ meta.path }{{$endraw}}     - The full path to the file (like "/posts/mypost.html")
        {{$raw}}{$ meta.url }{{$endraw}}      - The full URL (like "http://mysite.backliftapp.com/index.html")

- The **currentuser** variable: contains the settings and profile of the currently logged in user. Users must be logged in using the [Backlift auth API](/guides/authorization.html). 

- The **form** variable: contains the results of the latest form action. See the Backlift forms documentation for more information on how to submit forms. The Form variable contains a `success` property and for each field, `errors` and `data` properties.

        {{$raw}}{$ form.success }{{$endraw}}        
        {{$raw}}{$ form.<field>.errors }{{$endraw}} 
        {{$raw}}{$ form.<field>.data }{{$endraw}} 

- The **messages** variable: contains other messages which may result from form actions, such as "unauthorized" errors.     

## Adding metadata to files using the meta tag

Often times a document, like a webpage or a blog post, has some related data that's not part of the content. For example a blog post may have a title, author or post date. A webpage may have an abbreviated name that should be displayed in the navigation bar. This data can be added to documents by using the meta tag. Here's what it looks like:

    {{$raw}}{{$ meta }}
    title: "My first post"
    author: "Cole Krumbholz"
    {{$ endmeta }}{{$endraw}}

The meta tag should be placed at the top of a document. It contains a list of properties, each one has a property name, followed by a value. The syntax for the properties is YAML. There are a lot of very technical explanations of YAML online, but for the purpuses of the meta block, generally you can get by if you remember to:

1. Only use alphanumeric characters for keys ('title' and 'author' are OK, '@#$%' is NOT OK)
2. Follow the key with a ':' and a space. Note that if you forget the space you'll get a YAML parse error.
3. Folow that with the value, which can be a string, list or another dictionary of key, value pairs.

Here are some additional examples:

    {{$raw}}{{$ meta }}
    astring: "This is a string"
    alist: 
    - "first list item"
    - "second list item"
    adictionary:
      key1: "first value"
      key2: "second value"
    {{$ endmeta }}{{$endraw}}

For a quick intro to YAML check out [this guide on movable type](http://www.movabletype.org/documentation/developer/a-yaml-primer-yet-another-markup-language.html). 


