{{$meta}}
type: guide
title: Getting Started
order: 0
{{$endmeta}}

{{$layout /index.html as content}}

## Creating a website

You can create a new website with Dropbox using the [create new app](https://www.backlift.com/dashboard/createapp) form. Your app's files will be placed in the <code>/Apps/Backlift</code> folder of your Dropbox folder. While you are connected to the internet, any changes you make will automatically be synchronized to your Backlift website via Dropbox. 

Once the website has been created you will automatically be redirected to the website running on a special URL created by Backlift. If you need to visit your website later, find it in the [apps](https://www.backlift.com/dashboard/apps) section of your Backlift dashboard and click the "view app" link.

### Website templates

Backlift comes with a set of examples and starter templates. You can see the list of templates in the [create new app](https://www.backlift.com/dashboard/createapp) area of the dashboard.

### Your website's URL

Backlift websites are assigned a unique URL based on the name you provide when creating it. The format of the URL is:

	<name>-<random_string>.backliftapp.com

This URL is not easy to guess, so you won't have unexpected visitors to your website while it's still in development. However it's publicly accessible, so you can share your work with others by sending them the URL. 

Backlift also supports custom URLs using your own domain. You must have already purchased the domain from a third party service like [Name Cheap](http://namecheap.com) or [DNSimple](http://dnsimple.com). You can find instructions on how to enable your purchased domain name by visiting the [custom urls](http://backlift.com/dashboard/urls) section of the dashboard. (Note you need an upgraded account in order to use your custom DNS. See the [Upgrade](http://backlift.com/dashboard/upgrade) section of the dashboard)

### Project layout

Files and folders within your app's folder can be layed out any way you like. Any files you add to your app folder will be accessible via the same path from your app's URL. For example if you create an "about.html" file in the /pages folder of your app, that file will be available by browsing to:

    <your-app>.backliftapp.com/pages/about.html

## Backlift variables and the config.yml file

If you look at the index.html file from the simple-site template, you will see template variables, such as the {{$&nbsp;scripts&nbsp;}} and {{$&nbsp;styles&nbsp;}} variables. These variables help you maintain your website by keeping track of your project files in a central place, the config.yml file. When your html page is rendered, Backlift will substitute those variables with appropriate data from the config.yml file. For example, the {{$&nbsp;scripts&nbsp;}} variable will be replaced by a list of <code>&lt;script&gt;</code> tags, one for each file listed in the config.yml scripts variable.

<!--
For a list of Backlift variables, please see the [variables reference](variables.html).

For more information about the configuration options available in the config.yml file, see the [configuration reference](configuration.html).
-->

### The Static folder

Normally you may use Backlift variables in any html file that you add to your app. However, static files, like javascript, css and image files, shouldn't need to use the Backlift template variables. To speed up rendering of these pages, and to make better use of caching mechanisms, Backlift allows you to specify a static folder. Files within the static folder will not be rendered with the Backlift variables. The static folder can be set in your config.yml file. The default static folder is /public.

## Compilers and optimization

After your changes are uploaded via Dropbox or the CLI, Backlift will compile certain files and optionally optimize your javascript and css.

### Compilation phase

During the compilation phase, files with known extensions are compiled into javascript or css. The following is a list of the known file extensions, the type of file generated, and the associated compiler:

* .coffee --&gt; .js ([coffeescript](http://coffeescript.org/) compiler)
* .jst --&gt; .js ([underscore template](http://underscorejs.org/#template) compiler)
* .handlebars --&gt; .js ([handlebars](http://handlebarsjs.com/) template compiler)
* .less --&gt; .css ([less](http://lesscss.org/) compiler)

You can control which files are passed to the compilers by adding a `compile` directive to your project's config.yml file. The compile directive should include a list of files that will be compiled. The default, if no compile directive is specified, includes the following:

<pre><code class="no-highlight">compile:
- /**/*.jst
- /**/*.handlebars
- /**/bootstrap*.less
- /**/*.coffee
</code></pre>

(Note that only the main bootstrap.less file will typically be compiled. This is because the bootstrap.less file imports several .less components. Attempting to pass those component .less files directly to the less compiler will result in an error.)

Typically it will not be necessary to adjust the compile configuration. If no files of a particular kind exist, the compiler for that file type will not be invoked.

### Compilation results, Using templates

The .js and .css files generated by the compilation phase will be added to the {{$&nbsp;scripts&nbsp;}} and {{$&nbsp;styles&nbsp;}} Backlift variables. 

The scripts generated by the Underscore template compiler include code that adds the template to a global JST object. To use the template, simply call the function with the same name as the original template file, and pass it the template parameters to be rendered. For example, the file "profile.jst" will create a profile template function that can be used like so:

	var rendered = JST.profile({username: "Cole", hometown: "San Francisco"});
	$("#profile-div").html(rendered);

To use the scripts generated by the handlebars compiler, see the [handlebars documentation](http://handlebarsjs.com/). You will need to include the handlebars.js script in your config.yml scripts list. Here is an example of a "profile.handlebars" template in use:

	var rendered = Handlebars.templates.profile({
		username: "Cole",
		hometown: "San Francisco"
	});
	$("#profile-div").html(rendered);

### Optimization phase

During the optimization phase, javascript and css files are concatenated and minified. Optimization can be enabled or disabled using the `optimize` setting within config.yml.

	optimize: on

By default optimization is disabled. This reduces the time required to build your webapp, and avoides the obscure code generated by minification. Optimization should not be used during development.

Backlift uses the YUI compressor to minify and concatenate javascript and css files. The optimizer will generate two files, `\public\optimized-scripts.js` and `\public\optmized-styles.css`. The {{$&nbsp;scripts&nbsp;}} and {{$&nbsp;styles&nbsp;}} Backlift variables will include only these two files if the optimizer is enabled.

## The admin site

Each app gets a unique admin page that can be used for debugging and, in the future, managing security features like validation and authorization. You can view your app's admin page by clicking on the small gear icon under your app in the [apps](https://www.backlift.com/dashboard/apps) section of your Backlift dashboard.

As you develop your app, the admin page will change to reflect your app's collections and request history. This makes it a valuable debugging tool.
