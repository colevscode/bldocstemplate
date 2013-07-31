{{$meta}}
type: guide
title: Authorization
{{$endmeta}}

{{$layout /index.html as content}}

The authorization plugin provides an API for registering and establishing sessions for users, storing private and public user data, and setting permissions on data objects.

All passwords are salted and hashed before being stored using [bcrypt](http://bcrypt.sourceforge.net/). Currently all requests to backlift apps are encrypted with ssl using backlift's certificates. (All backlift app urls use the https:// protocol) User session cookies are encrypted and managed by the backlift app server via this API. 

## User data

Users are stored in a special collection within each app that can only be accessed through the auth API. A user model has the following schema (see [validation section](validation.html#validation-rule-reference) for type reference).

The user model:

    'id':           {'type': 'auto:unique_16_aA0', 'readonly': True}
    'username':     {'type': 'identifier', 'unique': True}
    'email':        {'type': 'email', 'unique': True}
    'groups':       {'type': 'list'}
    'profile':      <a profile model>

The user collection cannot be fetched. Only the single user model for the currently signed in user is accessible. Custom properties may be added to the user model which will remain private to the current user. For this reason, the user model is a good place to keep track of user preferences.

The user model contains a reference to a profile model. The profiles collection is separate and can be accessed like any other collection. (See the data persistence section in [basics](basics.html#data-persistence)) By default the name of the profile collection is 'profiles'. It's default permissions for each profile object match the system wide default permissions. (see permissions [below](#permissions-and-access-control)) Therefore profile models are publicly readonly by default, and fetching the profiles collection will yeild a list of user profiles. Custom properties may be added to a profile object, and they will be publicly accessible by default. For this reason, the profile object is a good place to store per-user public data.

The profile model:

    'id':           {'type': 'auto:unique_16_aA0', 'readonly': True}
    'username':     {'type': 'identifier', 'readonly': True}

Both the user and profile models include the special object properties specified [here](basics.html#special-object-attributes).

To get or set data for the current user, use the current user API:

    GET         /backlift/auth/currentuser 
    POST or PUT /backlift/auth/currentuser { <user model> }

If you are setting the user model, the data should be sent in the request body, and can either be a JSON object or a url-encoded set of key-value pairs. Not all the data fields must be set, only the included fields will be altered. Note that if you are setting the profile field, you must use JSON, not url-encoded data.

## Creating users and signing in

Backlift exposes the following API to facilitate user creation and management. The data should be sent in the request body, and can either be a JSON object or a url-encoded set of key-value pairs. 

*   **Creating users:** (POST /backlift/auth/register)

    You can create new users by sending a POST request to your app's /backlift/auth/register URL. The data you send should contain a 'username' attribute, an 'email' attribute, and a 'password' attribute or 'password1' and 'password2' attributes. 
        
        POST /backlift/auth/register {"username": "...", "email": "...", "password": "..."}

    There are several alternative API endpoints that permit creating users with just an email address, or requiring an email and username:

        POST /backlift/auth/registernoemail {"username": "...", "password": "..."}
        POST /backlift/auth/registeremailonly {"email": "...", "password": "..."}

    The response will contain a json object with the new user's data. 

*   **Logging in:** (POST /backlift/auth/login)

	To log in a user, POST to the login url with a pair of 'username' and 'password' attributes. They will be compared against the salted and hashed password store and if they match, the user will be logged in.

	When a user is logged in, two cookies will be set on the client, a session ID and a 'user' cookie that contains the user id.

*   **Logging out:** (POST or GET /backlift/auth/logout)

	To log out a user, POST or GET to the logout url.

	If you send a GET request, you can include a '_next' url parameter with a redirect url where the user will be sent after being logged out. For example the following link will log out a user and return them to the 'dashboard' page:

	    <a href="/backlift/auth/logout?_next=/dashboard"> Log out </a>

	By default the user will be redirected to '/' if logged out via a GET request.


## Permissions and access control

Backlift's authorization module allows you to control access to persisted objects. In backlift you authorize access to data, and build behaviors around that. A user profile object may be marked as readonly, which means that the server will reject attempts to update the profile object unless the currently logged-in user is the owner of that profile object.

Backlift uses the following attributes on objects in order to control access:

*   **_owner**: Whenever a user is logged-in, any objects created will have the _owner attribute set to the current user's id. This attribute is read-only and managed by the server.

*   **_groups**: A list of groups to which the object belongs.

*   **_public_permissions**, **_group_permissions**, **_owner_permissions**: Govern what actions are authorized on this object. It is a string containing any of the following characters. If one of the characters below is included in the string, it will grant the associated permission:
    
    * **'r'**: read   
    * **'w'**: write   
    * **'c'**: create  
    * **'d'**: delete

For example, the following object is only accessible to the owner:

	{ "_owner": "...", 
	  "_id": "...", 
	  "_owner_permissions": "rwcd",
	  "_group_permissions": "",
	  "_public_permissions": ""}

These are the default permissions that an object will be assigned, if the respective permissions attributes are missing on an object:

* owner: read, write, create, delete
* group: read, write
* public: read


## Using validation rules to set permissions

Authorization depends on validation to ensure that the model attributes responsible for controlling user access are correctly configured and not altered by malicious clients. Currently these rules are set using the config.yml file. Here are a few examples of how to correctly set permissions on your models using the 'collections' hash in the config.yml file:

*   Making the models of a collection readonly for the public:

        collections:
          tweets:
            message: {type: 'string', max: 140, required: yes}
            _public_permissions: {default: 'r', readonly: yes}

    In this example, tweets can be created and modified by users, due to default owner permissions. In this case we've explicitly specified that the public can only read tweets, not change them. Tweets currently only contain the message field, but could contain other attributes, such as the geo-location where the tweet was created.

*   Making a private user profile:

        collections:
          profiles:
            _public_permissions: {default: '', readonly: yes}

    This object cannot be read by the public. However the owner and members of this object's group will have access based on the default permissions.


