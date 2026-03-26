---
title: "Rails API authentication using cookies"
pubDate: 2026-03-25
description: "This is a tutorial on how to add cookie authentication to your rails API."
author: "Andrés Gracia Danies"
image:
  url: "/blog/rails-api-cookie-authentication.png"
  alt: "Rails logo"
tags: ["ruby on rails", "api"]
---

First, I want to welcome you to my first blog ever. This website is expected to be my personal portfolio and the place where I share learnings and thoughts.

My first post is about adding cookie authentication to your Rails app. This tutorial is more like a proof of concept that you can refine for your projects.

I chose to use Rails v8 built-in authentication instead of devise. The reason is that devise has so much magic behind and I was having trouble with the redirects it expected, while the Rails built-in authentication gives you every single file in a very minimalistic way that you can modify as you need in an easy way, as you will find in this tutorial. I think most of the steps shown here are useful if you want to use devise instead. Let's begin.

## Create a new Rails project

We are going to create a new project the usual way, don't forget to include the `--api` option.

Open your terminal and run the following command:

```bash
rails new my_api --api
```

## Generate authentication

Now let's add the Rails 8 built-in authentication as explained in the official [Rails Guides for Securing Applications](https://guides.rubyonrails.org/security.html#authentication) where you can find more details about it.

Once again, run these commands in your terminal:

```bash
rails generate authentication
rails db:migrate
```

So far, it's useful that you keep in mind that it will create a scaffold for Sessions and Password, a User model that has an `email_address` and a `password_digest`, and a controller concern for authentication.

## Configure session middlewares

In this step, we will include the middleware required for session management following the recommendations on the [Rails Guide for API-only Applications](https://guides.rubyonrails.org/api_app.html#using-session-middlewares).

Go to the file `config/application.rb` and add the following lines:

```ruby
# config/application.rb

# This also configures session_options for use below
config.session_store :cookie_store, key: "_your_app_session"

# Required for all session management (regardless of session_store)
config.middleware.use ActionDispatch::Cookies

config.middleware.use config.session_store, config.session_options
```

Don't forget to set the session key name!

## Modify application controller

At this point, if you try to run your Rails app, you will encounter an error related to some helper method. This happens because the API mode is a stripped down version of regular Rails, so we will need to include some modules for helpers, cookies and CSRF protection.

Go to the file `app/controllers/application_controller.rb` and add the following lines:

```diff
class ApplicationController < ActionController::API
+  include AbstractController::Helpers
+  include ActionController::Cookies
+  include ActionController::RequestForgeryProtection

  include Authentication
end

```

Now you can run your Rails app.

## Modify every redirect from the rails authentication and change for some json

The Rails 8 built-in authentication automatically generates a lot of redirections which are useful if you have a regular rails app. However, in API you want JSON responses instead, so let's modify that.

I'd recommend to use the search function of your IDE and look for `redirect_to` and then start changing one by one as every match has different messages and statuses (especially statuses are important). I know it's an annoying task but you'll need to make it once and you're done.

The changes will happen in three files: `authentication.rb`, `passwords_controller.rb` and `sessions_controller.rb`.

This is an example of how can it be done, but this is something that you might want to do different:

```diff
# sessions_controller.rb
def create
  if user = User.authenticate_by(params.permit(:email_address, :password))
    start_new_session_for user
-    redirect_to after_authentication_url
+    render json: { message: "ok" }, status: :ok
  else
-    redirect_to new_session_path, alert: "Try another email address or password."
+    render json: { message: "failed" }, status: :unprocessable_entity
  end
end
```

For the sake of this tutorial, use the previous code so you can reproduce the tests I perform late.

You can use AI to help generating the JSON responses, but always check it's giving the right status.

## Install and config rack-cors

Now, you need to allow requests from other domains or origins. So you will need to add the `rack-cors` gem to your Gemfile.

```bash
bundle add rack-cors
```

Then, add the domains to be allowed. In my case, I'm developing the frontend using React, so I'm allowing the localhost origin. You should also include the domains expected to serve the frontend in production.

Additionally, you have to include the `credentials: true` option to allow cookies.

```diff
# config/initializers/cors.rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
-    origins "example.com"
+    origins "http://localhost:5173"

    resource "*",
      headers: :any,
      methods: [ :get, :post, :put, :patch, :delete, :options, :head ],
+      credentials: true
  end
end
```

## Creating an endpoint to obtain a CSRF token

Finally, you have to create an endpoint to get the CSRF token.

Remember that a CSRF token is required for all non-GET requests, and it's tied to the user session, so you are going to need a token to log in, and once you are logged in you will need a new token, aaand once you log out you will need a new token if you want to log in again. Basically these are the three scenarios where you are required to refresh the CSRF token.

Let's create the endpoint:

```bash
rails generate controller csrf show
```

Maybe you want to use another name for the method, as `show` can be confused with a CRUD operation. But this works for me, maybe it will also work for you.

Then, you have to allow unauthenticated access to this method and return the token:

```diff
# app/controllers/csrf_controller.rb

+ allow_unauthenticated_access only: %i[ show ]

def show
+  render json: { token: form_authenticity_token }
end
```

Now you have to set the route for your new endpoint which in my case I liked `/csrf-token`:

```diff
# routes.rb
- get "csrf/show"
+ get "csrf-token" => "csrf#show"
```

## Test using your favorite HTTP client

Well, now we are ready to test that everything is working. You are expected to write your own tests for your app, but for this tutorial I will use Postman. Rails already wrote all the tests for sessions and passwords when you ran the `rails generate authentication` command, so you'll have to refactor them to expect JSON responses.

We are going to do the following tests:

- Get the CSRF token
- Log in as a User

Remember that the Rails 8 built-in authentication creates a User model but not the controller, so we are not going to create a User using a request, but manually in the Rails console.

So let's create a User. Open the Rails console:

```bash
rails console
```

And then create the new User:

```ruby
User.create!(email_address:"example@example.com",password:"secret_password")
```

### Obtaining the token

Now using Postman or your favorite tool, make a GET request.

**If you use Postman you can import the following curl requests by clicking File > Import and then paste the code**

```bash
curl --location 'http://localhost:3000/csrf-token'
```

It will return a JSON response with the token:

```json
{
  "token": "YOUR_CSRF_TOKEN"
}
```

This token will be used in the next step. Also, a cookie will be set in Postman.

### Logging in

Now that you have the token, make a POST request with the `email_address` and `password`. You will have to set the CSRF token in the `X-CSRF-Token`.

**If you use Postman you can import the following curl requests by clicking File > Import and then paste the code**

Note that if you are using Postman, it will automatically include the cookie in the header so the final result will look a bit different.

```bash
curl --location 'http://localhost:3000/session' \
--header 'X-CSRF-Token: YOUR_CSRF_TOKEN' \
--header 'Content-Type: application/json' \
--data-raw '{

        "email_address": "example@example.com",
        "password": "secret_password"

}'
```

Once you submit, you should get this response:

```json
{
  "message": "ok"
}
```

Just for the sake of testing, if you submit the wrong password, the response should look like this:

```json
{
  "message": "failed"
}
```

And that's it. Now you have a Rails API backend with cookie authentication that you can use with your favorite frontend framework.

### Javascript fetch script

I would also like to include a minimal example of how to do the test on Javascript. You can run these commands on your browser console but remember to be on the page allowed in the `cors.rb` file, otherwise you will get an error like `Access to fetch at 'http://localhost:3000/csrf-token' from origin 'http://example.com' has been blocked by CORS policy`, which is the expected behavior.

For the CSRF token endpoint,

```javascript
const res = await fetch("http://localhost:3000/csrf-token", {
  credentials: "include",
});

const data = await res.json();

console.log(data);
```

You should get the CSRF token and use it for logging in:

```javascript
const token = "YOUR_CSRF_TOKEN";

const res = await fetch("http://localhost:3000/session", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-CSRF-Token": token,
  },
  credentials: "include",
  body: JSON.stringify({
    email_address: "example@example.com",
    password: "secret_password",
  }),
});

const data = await res.json();
console.log(data);
```

And that's it. You should see the same JSON responses as in Postman.

## Final words and some tips

Well, as you can see, it's not too hard to enable cookie authentication in Rails API if you follow this tutorial. Now you can extend this base with the models and controllers you need, and the only thing yo need to make sure is that you are returning JSON data to your frontend.

The steps to obtain similar results should be very similar if you are using devise, but that's gonna be another tutorial in the future.

Here are a couple of useful things you can do once you finish this tutorial:

- Complete your User model with a scaffold so you can have the user controller and tests. Running a scaffold generator for the User model will give you errors, so you have to include in the command the attributes you want to add (i.e. `name:string`), the existing ones (`email_address:string password:digest`), and the options to prevent errors (`--skip-collision-check --skip`).

Here is the full command:

```bash
rails generate scaffold User name:string email_address:string password:digest --skip-collision-check --skip
```

- When a user signs up, you can automatically create a session so the user also logs in. Once you have the User controller, your `create` method can look like this:

```ruby
# user_controller.rb

def create
  @user = User.new(user_params)

  if @user.save
    # Create new session for the user
    User.authenticate_by(email_address: user_params[:email_address], password: user_params[:password])
    start_new_session_for @user

    render json: @user, status: :created
  else
    render json: @user.errors, status: :unprocessable_entity
  end
end
```

And last but not least, if you find any issues or have any comments, suggestions or anything else, please do not hesitate to contact me at graciadanies[at]gmail.com or via social network, I will be happy to improve this tutorial so it can be useful for anyone who wants to enable cookie authentication on their Rails API.
