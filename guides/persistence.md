{{$meta}}
type: guide
title: Data Persistence
{{$endmeta}}

{{$layout /index.html as content}}

In Backlift any app can store data on the server and retreive it later. This allows apps to implement complex functionality, such as collecting data from users via forms and visualizing that data in meaningful ways, or cordinating moves in a multiplayer game. You don't need to understand databases or SQL to store and retrieve data, just a basic understanding of javascript and jQuery. If you know how to fetch data jusing jQuery's ajax() methods, you can use the Backlift persistence API.

## Collections and models

Backlift uses just one data structure for persistence, the collection. A collection is just a group of things. Each thing in a collection is referred to as a model, and each model holds a set of related properties that describe the thing. A property has a name and a value. The models in a Backlift collection are not required to have the same properties.

For example, if you were building a library app, you might have a books collection. Each model in the collection would represent a single book, and might have a name, author, status and optional waiting property. Here's what such a collection would look like in Javascript Object Notation (JSON):

	{"books": [
		{"name": "The Corrections",
		 "author": "Jonathan Franzen",
		 "status": "checked-out",
		 "waiting": "Cole Krumbholz"},

		{"name": "Freedom",
		 "author": "Jonathan Franzen",
		 "status": "available"}
	]}

## The persistence API

Data is stored and retrieved using the persistence API. It's not a formal API beacause any URL that matches the pattern below is acceptable:

* GET /backlift/data/&lt;collection&gt;: will retrieve a list of the models in a collection.

* POST /backlift/data/&lt;collection&gt;: will create a new model and add it to a collection.

* GET /backlift/data/&lt;collection&gt;/&lt;item_id&gt;: will retrieve a specific model from a collection.

* PUT /backlift/data/&lt;collection&gt;/&lt;item_id&gt;: will update a model in a collection.

* DELETE /backlift/data/&lt;collection&gt;/&lt;item_id&gt;: will delete a model.

The data for PUT and POST requests should be sent in the request body, and can either be a JSON object or a url-encoded set of key-value pairs. 

Backlift stores the data as JSON documents, and does not need to know what properties a document will have in advance. In addition, collections do not need to be created before they are used. If a POST request is made for a collection the doesn't exist, Backlift will create it. If a GET request is made on a collection that doesn't exist, Backlift will return an empty set.

Later when you want to deploy your app in a production environment, you can setup validation via the config.yml config file. See the section on [data validation](validation.html) for documentation on how to "lock down" a collection.

## Persistence with Backbone.js

When using Backbone.js to communicate with the Backlift server, you just need to prefix your urls with "/backlift/data/". Backbone's default sync mechanism uses the URL scheme listed above. In fact, Backlift was built to allow backbone.js projects to work "out of the box". 

The following example shows how to set the url attribute of your collections and then use backbone's standard save and fetch methods.

	var MyBook = Backbone.Model.extend({});

	var MyBooks = Backbone.Collection.extend({
		model: MyBook,
		url: "/backlift/data/books"
	});

	var books = new MyBooks();
	books.fetch();   // will issue a GET /backlift/data/books

	var book = new MyBook({
		title: "The Corrections"
	});

	books.add(book);
	book.save();   // will issue a POST /backlift/data/books

## Persistence with HTML Forms

It's also possible use regular HTML forms to post data to Backlift API. This doesn't involve any Javascript, but might result to less optimal user experience. To send save data to Backlift with HTML forms, add these attributes to the form: `action="/backlift/data/<collection>"` and `method="POST"`.

As a security measure, you must also include `{{$ csrf}}` template tag somewhere inside the form element. This will add a hidden field to the form that helps the server to validate the origin of the data. If the field is not included, data will be rejected.

As an example, a form persisting data to Backlift might look something like this:

	<form method="POST" action="/backlift/data/mycollection">
		{{$&nbsp;csrf&nbsp;}}
		
		<input type="text" name="email" />
		<input type="text" name="name" />
		<input type="submit" value="submit" />
	</form>

After the form is submitted, current page is reloaded. You can add template tags {{$ formerrors }} and {{$ formsuccess }} that render errors and success messages that you would normally check with Javascript. If there are no errors or success messages, those template tags don't output anything. If there is, they render something like:

	{{$ formsuccess }} -> <div class="alert alert-success">Success</div>
	{{$ formerrors }} -> <div class="alert alert-error"><span>Error 1</span><span>Error 2</span></div>



## Special object attributes

Each time the backlift server persists a new object, a few attributes are applied automatically:

* **'id'**: If no id is set, the server will assign a unique uuid to each model and store it in the 'id' attribute. If there is an id, backlift will ensure that it is unique. 

* **'_owner'**: This attribute is automatically assigned to the currently logged-in user's id attribute, if there is a currently logged-in user. This value is read-only.

* **'_created'**: When a new object is created, this attribute is set with the current date and time in ISO standard format. This value is read-only.

* **'_modified'**: Each time an object is updated, this attribute is set with the current date and time in ISO standard format. This value is read-only.

* **'_id'**: Backlift mirrors the id attribute in an '_id' attribute. The '_id' attribute is used by backlift's mongodb database. In the future this attribute may be filtered out, so it's best not to rely on it.

### A note about dates

Backlift returns date/time values using the ISO 8601 format in UTC. These values can be parsed by Javascript's native Date object in all modern browsers. Here is an example of how to parse a date returned by backlift:

	// an example of ISO 8601 UTC time
    var obj = {_created: "2012-12-18T05:36:47.946Z"}; 

    var date = new Date(obj._created);

    console.log(date.toString());   // Mon Dec 17 2012 21:36:47 GMT-0800 (PST)
    console.log(date.toJSON()));    // 2012-12-18T05:36:47.946Z

The choice to go with ISO 8601 was somewhat arbitrary. There are several date time formats, and no particular format is prescribed in the JSON specification. We chose this format partly because it's what Date.toJSON() generates.