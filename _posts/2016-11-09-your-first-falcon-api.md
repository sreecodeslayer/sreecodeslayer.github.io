---
layout: post
title: Wrinting your First Python Falcon API
date: 2016-11-09T00:00:00.000Z
excerpt: A tutorial to help you write your first API.
project: true
tag:
  - Falcon
  - Python
  - API
comments: true
---

# Writing your first Python Falcon API

Whats up guys, well I have been super busy lately and hardly got any spare time to put together what I have been learning lately. Anyways, I would like to share my catch on using the super awesome Falcon for API based services. The reason Falcon is at the top is obviously due to its ultimately streamlined structure. This is clearly understandle if you are coming from Django or even from Flask frameworks or any of that sort. Since, Falcon is structured with API based works in mind, it doesn't come with any templating engine. But this can easily be solved using Jinja2 or any that you wish, though I don't see why anyone would head that way!

Alright then, let's dive into creating our first Falcon app that listens to some basic `GET` and `POST` requests handled by our server. Am going to be using a Python Virtual environment to work on the project, but you do without it. So fire up your favourite terminal, and let's get started.

1. We need to make a project folder inorder to work on our API.

  ```
  $ mkdir myapi
  ```

2. Now take you favourite terminal a drive into the newly created folder (`myapi`)

  ```
  $ cd myapi
  ```

3. Now setup a Virtual environment so that you can work within it. Am gonna be using `virtualenv` for this. If you got your choices, go ahead and fire it up as well.

  ```
  $ virtualenv venv
  $ . venv/bin/activate
  ```

  If you face an error where the terminal is probably stating that it doesn't know what the hell is `virtualenv`, just ask it to install through Python's `pip` (with proper permissions!).

  ```
  $ pip install virtualenv
  ```

  and then run the command again, you should be good to continue.

4. Now we need create the `<some crazy named>.py` that will be served by WSGI servers like Gunicorn. In the file, lemme call mine as `myapi.py` itself so that it makes it easier to relate.

5. Now import these two for now.

  ```python
  import falcon
  import json
  ```

6. So, in Falcon, our api apps are nothing but instances of the falcon.API() which are run WSGI Servers. So next we need to do the same for our `myapi` app.

  ```python
  api = falcon.API()
  ```

7. Now we create a Resource class that is going to handle the requests.

  ```python
  # ... imports and api instance ...
  class MyAPI(object):
  # ... class body ...
  ```

8. To handle the `GET` requests, we need to define `on_get()` function. So let's see how this is done.

  ```python
  class MyAPI(object):
    def on_get(self, req, resp):
      # ... do the fun here ...
  ```

9. Since there is nothing magical happening in this app of ours, let's just say everything is fine with request, and hence sent a `HTTP 200, OK` response. To do this,

  ```python
  def on_get(self, req, resp):
    resp.status = falcon.HTTP_200 # all good : OK
    resp.body = json.dumps({
      'message' : 'GET request is received and this is my response.'
    })
  ```

10. All that we need to do now is add a url route so that we can map our Resource class to do the job when that url(route) is requested by the client. To do this,

  ```python
  api.add_route('/myapi',MyAPI())
  ```
11. With this, the app will basically respond to the `GET` request on `/myapi` url route.
12. Before firing up the server, we need to install the required packages `falcon` that we imported as we are on a fresh virtualenv. For those who aren't, you might be ok without the steps below.
13. I normally use the `pipreqs` to generate the traditional `requirements.txt` file opposed to the pip freeze. `pipreqs` looks into the project import statements and creates the requirements.
14. Take the teminal and install `pipreqs` if you haven't by (assuming proper permissions!) `$ pip install pipreqs`
15. Assuming you have not wandered off from the project root in the terminal, type this command to generate requirements file using `pipreqs`
16. Now `$ pip install -r requirements.txt`
17. Go ahead and run your app. `$ gunicorn myapi:api`
18. Visit (http://127.0.0.1:8000/myapi)

I suggest you using a good REST Client like [Insomnia RESTClient](https://insomnia.rest/) to test your APIs.