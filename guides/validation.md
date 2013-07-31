{{$meta}}
type: guide
title: Data Validation
{{$endmeta}}

{{$layout /index.html as content}}

## Backlift collections and schemas

Collections are like tables in a database and are defined by a set of attributes, or fields. In backlift individual models in a collection don't need to contain all the attributes of their sibling models. However, if any of the models in a collection contain an attribute, it will be considered a part of that collection's schema.

By default there are no validation rules on any models in backlift. Anything can be stored in a collection, and new collections can be created on the fly. This is useful during development when the data model of an application is changing regularly. Later, when the application is ready for production, schema validation rules may be applied to "lock down" the kinds of data that backlift accepts.

## Defining schemas

Currently backlift validation rules must be created using the config.yml configuration file. Here is an example section of the config.yml file that sets up a validation rule:

    collections:
      tweets:
        message: {type: 'string', max: 140, required: yes}
        (other rules...)
      (other collections...)

Each sub group under 'collections' defines a new collection. When an AJAX request is sent to a /backlift/data/collection URL, backlift searches for the collection in this list. When found, the data attributes are matched against the rule names in order to select validation tests. If the data passes the tests for all attributes, the operation is permitted. 

For a discussion of the permissions attributes used during authorization, see the [authorization](authorization.html) section.

The syntax for defining a new rule is:

    <field name>: {[type: '<rule type>',] <parameter name>: <value> [...]}

If the rule 'type' is omitted, it will be assumed to be a 'string'.

## Validation errors

If one or more validation tests fail, backlift will return a 400 response along with a JSON object containing error data. The error data structure will look like this:

    {"form_errors": {
    	"message": ["exceeds 140 character limit"],
    	"otherfield": ["required"],
    	"": ["other problems"],
    }}

Each attribute of the 'form_errors' hash is a field name, and the value is a list of error messages. The empty string attribute indicates form-wide errors.

## Validation rule reference

The following is a list of validation rule types, and their type specific parameters.

*   '**string**' (min, max):
    A string with an optional minimum and maximum length. This is the default validation type for all rules.

*   '**regex**' (regex):
    A string that must match a regular expression. 

*   '**identifier**':
    A strictly alpha-numeric string at least 2 characters long and no more than 30 characters long. (It must match the regular expression '^[a-zA-Z0-9]{2,30}$') In addition identifiers are forced to lower-case when validated. Identifiers are used primarily for things like usernames and app IDs.

*   '**password**': 
    A string at least 8 characters long and no longer than 30 characters.

*   '**email**':
    Must be a valid email address.

*   '**list**':
    Must be a strig of the form '["item", "item", ...]'. Allows string representations of lists to be stored and retreived without requiring json deserialization.

*   '**auto:uuid**':
    A string that matches the regex pattern '^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$'. A random uuid matching this pattern will be substituted for this attribute's value when the object is initially created.

*   '**auto:unique_uuid**':
    A string that matches the regex pattern '^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$'. A random uuid will be substituted for this attribute's value when the object is initially created. This value will be unique amongst values for this attribute on this collection.

*   '**auto:random_16_a0**':
    see below

*   '**auto:random_16_aA0**':
    see below

*   '**auto:unique_16_a0**':
    see below

*   '**auto:unique_16_aA0**':
    A string that matches the regex pattern '^[a-zA-Z0-9]{16}$' or '^[a-z0-9]{16}$'. A random string containing 16 alphanumeric characters (just lowercase, or upper and lowercase), will be substituted for this attribute's value when the object is initially created. If unique is specified the value will be unique amongst values for this attribute on this collection.

This list is incomplete at this time. We will update this list as new validation rules are created.

We realize that it is impossible to perform all server side data validation using a predetermined finite set of validation rules. Our goal is not to cover all possibilities. We are currently working to define a reasonable set that covers as much territory as possible. We are also evaluating alternatives for how to handle more custom validation needs.

## Common validation rule parameters

All validation rules accept the generic parameters 'default', 'required' and 'readonly'. 

* The 'default' parameter sets the value if no value is specified for an attribute.

* The 'required' parameter will ensure that a value is supplied for an attribute if set. (The value cannot be the empty string, nor can the attribute be absent entirely.)

* The 'readonly' parameter will cause the server to quietly reject any changes made to that attribute if set.

A common pattern is to set a value for the 'default' parameter, and set 'readonly' to 'yes'. This has the same effect of setting a fixed value for an attribute. All new objects created with these parameters will have the default value set for this attribute, which cannot be changed. This technique is effective for setting permissions on objects. See the [authorization](authorization.html) section for further discussion.

