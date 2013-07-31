{{$meta}}
type: reference
title: Authorization API
{{$endmeta}}

{{$layout /index.html as content}}

### Register

    POST /backlift/auth/register  
    POST /backlift/auth/registernoemail
    POST /backlift/auth/registeremailonly

Creates a user. See the section on [authorization](authorization.html) for more detail.

Parameters: (see [validation reference](validation.html#validation-rule-reference))

    // regsiter and registernoemail
    username: {type: 'identifier', required: yes, unique: yes}     
    // register and registeremailonly
    email: {type: 'email', required: yes, unique: yes}             
    password: {type: 'password', required_if_empty: 'password1'}
    password1: {type: 'password', required_if_empty: 'password'}
    password2: {type: 'password', must_equal: 'password1'}

Returns: The created user model

Example using AJAX:

    $.ajax({
        type: "POST",
        url: "/backlift/auth/register", 
        data: {
            username: "username",
            email: "user@example.com",
            password1: "password",
            password2: "password"
        },
        success: function(result) { 
            console.log(result);
        } 
    });

    // result: 
    // {
    //    "id":"JPwLMxPHmJDkGpwk"
    //    "email": "user@example.com",
    //    "username": "username",
    //    "groups": ["JPwLMxPHmJDkGpwk"],
    //    "_modified": "2012-12-17T07:18:23.823617Z",
    //    "_created": "2012-12-17T07:18:23.823603Z",
    //    "profile": { 
    //        "username": "username",
    //        "_created": "2012-12-17T07:18:24.574915Z",
    //        "_modified":"2012-12-17T07:18:24.574940Z",
    //        "_owner": "JPwLMxPHmJDkGpwk"
    //    }
    // }

Example using a form:

    <form method="POST" action="/backlift/auth/registernoemail">
        Username: <input type="text" name="username">
        Password: <input type="password" name="password">
        <input type="hidden" name="_next" value="/home">
        <input type="submit">
    </form>



### Login

    POST /backlift/auth/login

Signs in a user. See the section on [authorization](authorization.html) for more detail.

Parameters: (see [validation reference](validation.html#validation-rule-reference))

<pre><code class="javascript">username: {type: 'identifier', required_if_empty: 'email'}
email: {type: 'email', required_if_empty: 'username'}
password: {type: 'password', required: yes}
</code></pre>

Example using AJAX:

    $.ajax({
        type: "POST",
        url: "/backlift/auth/login", 
        data: {
            username: "username",
            password: "password"
        },
        success: function(result) { 
            console.log(result);
        } 
    });

    // result: (same as register)
    // {
    //    "id":"JPwLMxPHmJDkGpwk"
    //    "email": "user@example.com",
    //    "profile": { 
    //        "username": "username",
    //        "_owner": "JPwLMxPHmJDkGpwk",
    //        ...
    //    },
    //    ...
    // }

Example using a form:

    <form method="POST" action="/backlift/auth/login">
        Username: <input type="text" name="username">
        Password: <input type="password" name="password">
        <input type="hidden" name="_next" value="/home">
        <input type="submit">
    </form>
