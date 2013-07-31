{{$meta}}
type: reference
title: API Overview
order: 0
{{$endmeta}}

{{$layout /index.html as content}}

This is a reference for the Backlift API. Each API entry will have a definition, for example:

    GET /backlift/auth/currentuser

This specifies that a GET request can be sent to the /backlift/auth/currentuser URL. 

All requests should use the app's domain as the host name. For example, the app <code>myapp-1ja0s</code> should send the above request to:

<pre><code class="no-highlight">https://myapp-1ja0s.backliftapp.com/backlift/auth/currentuser</code></pre>

In practice, requests using jQuery $.ajax functions can leave off the domain name, and just send requests to /backlift/auth/currentuser like so:

<pre><code class="javascript">$.ajax({
    type: "GET",
    url: "/backlift/auth/currentuser",
    success: function(result) { 
        console.log(result); 
    }
});  
</code></pre>  

All requests must be sent via https. Some requests require a logged in user or are effected by the current user session. The user session is managed using the auth login and logout APIs.

For PUT or POST requests, parameters must be sent in the body of the request and can either be a JSON object, or a list of URL encoded parameters. All responses will be JSON.

