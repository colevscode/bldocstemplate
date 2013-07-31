{{$meta}}
type: reference
title: Data API
{{$endmeta}}

{{$layout /index.html as content}}

### Retrieve all models in a collection

    GET /backlift/data/<collection>

Returns a list of models in a collection. See the section on [persistence](persistence.html) for more detail.

Example usage:

    $.ajax({
        type: "GET",
        url: "/backlift/data/todos",
        success: function(result) { 
            console.log(result); 
        }
    });

    // result: 
    // [{
    //    "id":"8c064fd0-616f-4e18-91e5-959b5475516f",
    //    "title":"Do something",
    //    "order":1
    //    ...
    // },{
    //    "id":"4928bcd1-56f7-40e4-ba78-212bae096b5c",
    //    "title":"Something else",
    //    "order":2
    //    ...
    // }]


### Create a new model

    POST /backlift/data/<collection>

Creates a new model for a collection. See the section on [persistence](persistence.html) for more detail.

Parameters:

* **id**: the id of the new object. If no id is supplied, the object will be assigned a random id. 
* ... any additional parameters passed will become properties of the new model

Returns: A copy of the new model that will include a new id property if one has been assigned.

Example:

    $.ajax({
        type: "POST",
        url: "/backlift/data/todos", 
        data: {
            title: "Write Code",
            order: 3
        },
        success: function(result) { 
            console.log(result);
        } 
    });

    // result: 
    // {
    //    "id":"4928bcd1-56f7-40e4-ba78-212bae096b5c",
    //    "title":"Write Code",
    //    "order":"3",
    //    ...
    // }

Errors:

* **400 Bad Request**: If the data submitted doesn't pass one of the validation tests, the response will be a 400 error. The result will be a JSON object with a 'form_errors' property that contains a dictionary where the keys are names of the property for which a validation error was raised, and the values are lists of the validation errors for each property. For example:

      {"form_errors":
          "title": ["must be 25 characters or less"]
      } 

* **403 Not Authorized**: This error occurs if the currently signed-in user doesn't have permission to perform this operation. This can occur if the model has an id that matches an existing model in the collection owned by another user. It can also occur if the collection's schema has an _owner_permissions attribute with a default that lacks create permissions. See [permissions and access control](authorization.html#permissions-and-access-control) or more information.

### Retrieve a model from a collection

    GET /backlift/data/<collection>/<model_id>

Returns a models. See the section on [persistence](persistence.html) for more detail.

Example usage:

    $.ajax({
        type: "GET",
        url: "/backlift/data/todos/8c064fd0-616f-4e18-91e5-959b5475516f",
        success: function(result) { 
            console.log(result); 
        }
    });

    // result: 
    // {
    //    "id":"8c064fd0-616f-4e18-91e5-959b5475516f",
    //    "title":"Do something",
    //    "order":1
    //    ...
    // }


### Update a model

    PUT /backlift/data/<collection>/<model_id>

Updates a model. See the section on [persistence](persistence.html) for more detail.

Parameters:

* **id**: the id of the new object. If no id is supplied, the object will be assigned a random id. 
* ... any additional parameters passed will become properties of the new model

Returns: The updated model.

Example:

    $.ajax({
        type: "PUT",
        url: "/backlift/data/todos/4928bcd1-56f7-40e4-ba78-212bae096b5c", 
        data: {
            title: "Write MORE Code",
            order: 1
        },
        success: function(result) { 
            console.log(result);
        } 
    });

    // result: 
    // {
    //    "id":"4928bcd1-56f7-40e4-ba78-212bae096b5c",
    //    "title":"Write MORE Code",
    //    "order":"1",
    //    ...
    // }

Errors:

* **400 Bad Request**: If the data submitted doesn't pass one of the validation tests, the response will be a 400 error. The result will be a JSON object with a 'form_errors' property that contains a dictionary where the keys are names of the property for which a validation error was raised, and the values are lists of the validation errors for each property. For example:

      {"form_errors":
          "title": ["must be 25 characters or less"]
      } 

* **403 Not Authorized**: This error occurs if the currently signed-in user doesn't have permission to perform this operation. This can occur if the model to be updated is owned by another user, and the _public_permissions property excludes write permission. See [permissions and access control](authorization.html#permissions-and-access-control) or more information.


### Delete a model

    DELETE /backlift/data/<collection>/<model_id>

Removes a model from it's collection. See the section on [persistence](persistence.html) for more detail.

Example:

    $.ajax({
        type: "DELETE",
        url: "/backlift/data/todos/4928bcd1-56f7-40e4-ba78-212bae096b5c", 
        success: function(result) { 
            console.log("Deleted!");
        } 
    });

Errors:

* **403 Not Authorized**: This error occurs if the currently signed-in user doesn't have permission to perform this operation. This can occur if the model to be updated is owned by another user, and the _public_permissions property excludes delete permission. See [permissions and access control](authorization.html#permissions-and-access-control) or more information.

