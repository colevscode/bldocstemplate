{{$meta}}
type: guide
title: Tunnel API
{{$endmeta}}

{{$layout /index.html as content}}

> Note: the tunnel API has recently been added, and we are still testing it. It should be considered "beta" functionality.

## Introduction

The Backlift Tunnel API lets you to access 3rd party APIs easily just by using Javascript. There are two major challenges the Tunnel API solves:

1. Browsers don't let you send AJAX calls to other domains. This is called the same-origin policy and requires work arounds like Cross Origin Resource Sharing (CORS).
2. There are (secret) API keys you don't want expose to all your users.

Basically, when you use Tunnel API, the Backlift server makes the request on your behalf (it *proxies* the request), and then returns the results for you. Since requests from the browser are sent to the same domain as the site itself, there are no CORS problems. Also, it's possible to inject additional variables to those requests by using the config.yml, so there's no need to put secret keys into Javascript files.

## Getting started

To get started, you must enable tunnels in your app's config.yml. Open up the file and add:

    tunnels:
        exampleapiname:
            base_url: 'http://example.com/api/'
        someothersite:
            base_url: 'http://someother.com/api/'

Under `tunnels` we define external APIs that are accessible via the Tunnel API. Above, we define the `exampleapiname` and `someothersite` tunnels. Both have a `base_url`, which is the base URL where requests to those services are sent.

You use Tunnel API by issuing AJAX requests to `/backlift/tunnel/*servicename*/*uri*`. *Servicename* maps to a certain base URL, and anything that comes after that is appended to the request URL. For example:

<pre><code class="javascript">$.get('/backlift/tunnel/exampleapiname')
// -> GET http://example.com/api/

$.get('/backlift/tunnel/exampleapiname/users')
// -> GET http://example.com/api/users

$.get('/backlift/tunnel/someothersite')
// -> GET http://someother.com/api/
</code></pre>  

Of course, you are free to use other HTTP verbs as well. All `GET`, `POST`, `PUT` and `DELETE` requests to Tunnel API are sent to the 3rd party service with the same method. For example `$.post('/backlift/tunnel/exampleapiname')` turns into `POST http://example.com/api/`.

## Request parameters

### Parameters

All variables you add to the request sent to `/backlift/tunnel/` are also forwarded. For example:

<pre><code class="javascript">$.get('/backlift/tunnel/exampleapiname/all', {limit: 10})
// -> GET http://example.com/api/all?limit=10

$.post('/backlift/tunnel/exampleapiname/users', {name: 'laurihy', company: 'backlift'})
// -> POST http://example.com/api/users 'name=laurihy&company=backlift'
</code></pre>  

Depending on the request type, we either add data to request URL (GET) or to body (POST and PUT)

Note, that by content-type is also preserved, and that by default for POST and PUT jQuery sends the body as form-encoded data. If you want to send JSON, you have to specify content type to be `application/json` for the request.

<pre><code class="javascript">$.ajax({
    url: '/backlift/tunnel/exampleapiname/users',
    method: 'POST',
    contentType: 'application/json',
    data: JSON.stringify({name: 'laurihy', company: 'backlift'})
});

// -> POST http://example.com/api/users '{"name":"laurihy", "company":"backlift"}'
</code></pre>  

### Headers

Sometimes you need to set specific headers for the request, so we also pass those forward. To set headers of ajax requests, you can do something like:

<pre><code class="javascript">$.ajax({
    url: '/backlift/tunnel/exampleapiname',
    method: 'GET',
    headers: {
        'X-Custom-Header': 'Foo',
        'X-Second-Header': 'Bar',
    }   
});
</code></pre>  

## Injecting parameters and headers from config.yml

It's pretty common to have some kind of secret tokens or API keys that are needed for the requests, but shouldn't be exposed to your app's users. As promised earlier, we let you inject variables to requests also by using the app's config.yml. All variables defined in config.yml override the ones sent from browser, so if config.yml sets variable foo=Bar and browser foo=Hack, the request will contain foo=Bar. To inject variables, you can add `params` under the service they belong to. In config.yml:

    tunnels:
        exampleapiname:
            base_url: 'https://example.com/api/'
            params:
                secretkey: 'MySecretKey'
        someothersite:
            base_url: 'http://someother.com/api/'

Now, all requests to exampleapiname will also include MySecretKey, without the browser ever being exposed to it.

<pre><code class="javascript">$.get('/backlift/tunnel/exampleapiname')
// -> GET https://example.com/api/?secretkey=MySecretKey
</code></pre>  

Similarly, you can also inject headers by specifying `headers` in config.yml:

    tunnels:
        exampleapiname:
            base_url: 'https://example.com/api/'
            params:
                secretkey: 'MySecretKey'
            headers:
                X-My-Header: 'MyCustomHeader'
        someothersite:
            base_url: 'http://someother.com/api/'
            
> **SECURITY NOTE**: Setting the base_url to an HTTPS endpoint will ensure that the communication between the backlift server and the external endpoint is secure. However the data from the request will be returned over whatever protocol is used in the tunnel request. So in the above example, the `$.get` request to `/backlift/tunnel/exampleapiname` will result in a secure request from backlift to example.com, protecting the secret key, however the data returned will be sent in the clear from backlift's server to the browser. To ensure that the data is also sent securely be sure that your Backlift site is using ssl encryption. 


## Dynamically bind variables to current user

One useful addition to being able to define parameters and headers in config.yml, is the ability to dynamically bind them to currently logged in Backlift user. Variables between `{{` and `}}` are interpreted as attributes of the currently logged in user. If there is no user, or user doesn't have that attribute, the variable is left empty. In config.yml:

    tunnels:
        exampleapiname:
            base_url: 'https://example.com/api/'
            params:
                username: {{username}}
            headers:
                X-User-Token: {{profile.token}}

Now, if I would be logged in with my user, requests would turn into something like this:

<pre><code class="javascript">Backlift.current_user = {
    username: 'laurihy', 
    profile: {
        token:'myapitoken',
        othervar:'huu'
    } 
}

$.get('/backlift/tunnel/exampleapiname')
// -> GET https://example.com/api/?username=laurihy
//        
//        Headers: 'X-User-Token: myapitoken'…
</code></pre>  

It's also possible to bind user variables into base_url. A nice usecase might be something like:

    tunnels:
        exampleapiuser:
            base_url: 'https://example.com/api/users/{{username}}/

Then:

<pre><code class="javascript">$.get('/backlift/tunnel/exampleapiuser')
// -> GET https://example.com/api/user/laurihy
</code></pre>  

## Authentication

For authenticating requests to 3rd party APIs, Tunnel API provides support for Basic auth. Just add `auth` under a service name with a username and password, and all requests to that base_url will get authenticated. In config.yml:

    tunnels:
        exampleapiname:
            base_url: 'https://example.com/api/'
            auth:
                basic_user: 'laurihy'
                basic_pass: 'mysecretpassword'


Will authenticate requests like this:

<pre><code class="javascript">$.get('/backlift/tunnel/exampleapiname')
// -> GET https://example.com/api
// 
//        Headers: 'Authorization: Basic QWxhZGRp… 
</code></pre>  

Read more about HTTP Basic auth from [http://en.wikipedia.org/wiki/Basic_access_authentication](http://en.wikipedia.org/wiki/Basic_access_authentication)

We'll add support for OAuth in the near future.

## Access control for tunnels

Especially if you're exposing API endpoints that let users authenticate using your API keys, you might want to allow access to only administrators of your app (essentially you) or perhaps at least require users to log in. Two parameters we use for this are `auth_required` and `groups_required`. In config.yml:

    tunnels:
        exampleapiname:
            base_url: 'https://example.com/api'
            auth_required: yes
            groups_required: 'administrators'

This requires all requests to `/backlift/tunnel/exampleapiname` to be made by authenticated *backlift* users that are also administrators. `auth_required` can either be `yes` or `no`. If it's no, then all users (also non-registered) can access that service. `groups_required` is a string that matches a group name that is required from logged in users.