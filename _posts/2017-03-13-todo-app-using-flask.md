---
layout: post
title:  "To-Do application using Python-Flask"
date:   2017-03-13
excerpt: "A to-do application to learn the Python-Flask framework, Bootstrap and AngularJs."
project: true
tag:
- python 
- flask
- bootstrap
- angularjs
comments: true
---
Well, hello everyone, in this post, we will look into writing a To-Do application using the famous Micro Framework, [Python-Flask](http://flask.pocoo.org/), [Bootstrap](https://getbootstrap.com/) and [AngularJs](https://angularjs.org/). So as we started in our [previous](http://www.sreenadhtc.net/your-first-falcon-api/) tutorial, we will create a new project directory and work on a virtual environment.  
so for this, use the following commands:  

```bash  
$ mkdir todo-flask  

$ cd todo-flask  

$ virtualenv ve  

$ . ve/bin/activate  
```  
Once you have done this, we can now start installing the project requirements. This for now, will only be `python-flask`  

```bash  
$ pip install flask
```  
After the installation, we can test if flask is installed and is ready to used in our code. So, inside the root folder of the project, create a python file, which is going to be the main app script. So am gonna call this, `app.py`. Inside this file,  

```python  
from flask import Flask, jsonify
app = Flask(__name__)

@app.route("/")
def index():
    return jsonify({'message':'Backend is up!'})

if __name__ == '__main__':
    app.run(debug=True, threaded=True, host='0.0.0.0') 

```  
Now, take your favourite terminal application, navigate to the root project directory, and launch the app. To do this  

```bash  
$ python app.py  
```  
or  

```bash  
$ gunicorn app:app --reload  
```  

> If you are using Gunicorn, make sure you do `pip install gunicorn`, before the command above.  

Now, open up the link in browser [first command](http://0.0.0.0:5000/) or [second command](http://127.0.0.1:8000/), depending upon which method you chose to run the app. Check if you are getting the message as a JSON, with a content saying "Backend is up!"  
Now, let's add a default, normalizing css which makes sure everything is responsive and mobile friendly. You can copy paste the contents from [this file](https://github.com/sreecodeslayer/todo-flask/blob/df3827ab2e8efb6b450ca85fcd2cc21cc45e68b1/static/css/app.css) or you can go over to the actual [repository](http://github.com/necolas/normalize.css) and follow on how to use it in other ways.  
You should put the css file inside `static/css` folder so that Flask will automatically send these files whenever we use it in our HTML files. If you want different project structure, take a look at `Flask-versioned`.  
Now we are gonna start adding templates and correspondingly update our `app.py` to send those template(s) whenever we fire an http request to the Flask server. To do this we need to create another default folder for templates, called `templates` and put our `index.html` inside that folder.  
So, by now your project folder would probably of this structure:  

```  
project
	|
	- static
	|	|
	|	-css
	|		|
	|		-app.css
	- templates
	|	|
	|	-index.html
	|
	- ve (virtual env folder, ignored from here on)

```

Now,let's add some content to our `index.html` and make our first http request and see what happens!  
So, go ahead and add your own html contents if you wish, else paste these lines from below code snippet  

```html
{% raw %}<!DOCTYPE html>
<html>
<head>
	<title>Todo - A Flask learn app</title>
	<meta name="viewport" content="width=device-width, initial-scale=1">

	<!-- Add app.css -->
	<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/app.css') }}">
</head>
<body>
<h1>Welcome!</h1>
</body>
</html>{% endraw %}
```  
Now, as you might have noticed, we have setup the home page here, but we haven't actually told the server (Flask app), to serve this html when the user visits the source url or '/' path. So, to do this, let's modify our `app.py` as:  

```python
from flask import Flask, jsonify, render_template, url_for
app = Flask(__name__)

@app.route("/")
def index():
    return render_template('index.html')

if __name__ == '__main__':
    app.run(debug=True, threaded=True, host='0.0.0.0')
```

> Notice the changes we made here, we added the function `render_template` to send the template file (index.html) and `url_for` (to tell jinja2 to appropriately fix the resource urls that we added in html code snippet just above this.)  

Now, if we refresh the browser, we will be presented with the awesome index page that we just wrote. You can start modifying this page to your liking and once you have done that, we will continue writing our app.  

Next thing to do, before we start writing our Angular Frontend is to actually bring the resources needed to setup an Angular Frontend. So, let's do that now, 
Make a folder for all your javascript files, say `js` inside the `static` folder. Am gonna call my angularjs file as `app.js`.

```javascript
var tApp = angular.module('todoApp', ['ngRoute'],function($interpolateProvider){
	$interpolateProvider.startSymbol('[[');
    $interpolateProvider.endSymbol(']]');
});
tApp.controller('TodoController', ['$scope', '$http', '$location', '$route', function($scope, $http, $location, $route) {

}]);
```

Next, we need to add this to our `index.html` to fire up the `controller`. So,  

```html  
{% raw %}<!DOCTYPE html>
<html ng-app="todoApp">
<head>
	<title>Todo - A Flask learn app</title>
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="icon" href="{{ url_for('static', filename='images/fav.ico') }}"/>
	<!-- Add app.css -->
	<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/app.css') }}">
	<!-- JQuery -->
	<script src="https://code.jquery.com/jquery-3.1.1.slim.min.js"></script>
	<!-- Bootstrap -->
	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"/>
	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
	<!-- Angular magic goes here -->
	<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.1/angular.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.1/angular-route.min.js"></script>
	<script type="text/javascript" src="{{ url_for('static', filename='js/app.js') }}"></script>
</head>
<body class="bg" ng-controller="TodoController">{% endraw %}
```
In the above code snippet, you can see I have added a lot of stuffs, let's go around them one by one. So, firstly, we need jQuery for bootstrap to work, secondly we need to add the AngularJs libraries. Also I mentioned this tutorial will be using Bootstrap to theme the pages, so that's added as well. After all these dependencies are taken care of, we have to add `ng-app="todoApp"` and the angular app controller to the html, which is what the `ng-controller="TodoController"` does. If everything went well, your next browser refresh should successfully set everything we need to work with.  

So, without further adeiu, let's start writing out APIs.  
Let's write our first API controller function on the frontend, which is obviously for user login:  

```javascript
tApp.controller('TodoController', ['$scope', '$http', '$location', '$route', function($scope, $http, $location, $route) {
	$scope.login = function(){
		$http({
			url: '/login',
			method: 'POST',
			data: JSON.stringify({
				'username':$scope.login.username,
				'password':$scope.login.password
			}),
			headers: {'Content-Type':'application/json'}
		}).then(function(response){
			console.log(response['status'] + ' ' + response['statusText']);
		});
	}
```
For user input, we will use Bootstrap modals and forms. So let's do that next. Add these lines to your `index.html`:  

```html
{% raw %}<div id="main-wrapper" class="bg">
		<div class="card card-1">
			<h5 class="text-muted">Howdi user,<br>Login/Signup and start ticking off those tasks!</h5>
			<div class="pull-right">
				<button data-toggle="modal" data-target="#loginModal" class="btn btn-primary">Login</button>&nbsp;
				<button data-toggle="modal" data-target="#signupModal" class="btn btn-success">Signup</button>
			</div>
		<!-- </div> -->
		<!-- About Modal -->
		<div class="modal fade" id="aboutModal" role="dialog">
			<div class="modal-dialog">
				<div class="modal-content">
					<div class="modal-body">
						<h5 class="lead text-center text-muted">About</h5>
						<p class="text-info">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed porttitor mauris a dignissim molestie. Aenean id ante commodo, consequat metus id, mattis nisl. Nulla quis tortor imperdiet, molestie nisl vel. </p>
						<p class="text-info">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce accumsan odio vel dolor lacinia, sit amet mollis dui sodales. Duis gravida magna eget tristique efficitur. Aenean dignissim nisl vel enim interdum viverra. Duis metus lorem, facilisis quis ornare a, porttitor.</p>
					</div>
				</div>
			</div>
		</div>
		<!-- Login Modal -->
		<div class="modal fade" id="loginModal" role="dialog">
			<div class="modal-dialog" >
				<div class="modal-content">
					<div class="modal-body">
					<h5 class="lead text-center text-muted">Login</h5>
					<form ng-submit="login()">
						<div class="form-group">
							<label class="text-success">Username: </label>&nbsp;<input class="form-control" ng-model="login.username" type="text" name="username">
						</div>
						<div class="form-group">
							<label class="text-success">Password: </label>&nbsp;<input class="form-control" ng-model="login.password" type="password" name="password">
						</div>
						<div class="form-group">
							<input class="btn btn-success" type="submit" name="login" value="Login">
						</div>
					</form>			
					</div>
				</div>
			</div>
		</div>
</div>
{% endraw %}
```
You can skip this above step if you have better ideas, but make sure you make appropriate changes to the controller functions and scope variables.  
Now, we need catch this request in our Flask app and give back appropriate response back to the controller function.  
As you can see, this app has user accounts, this means, we need to use database and User modals. All this can be done easily by using MongoEngine that takes care of writing data to the MongoDB, while you use the simpler ORM (Object Relational Mapping) much like that in Django. For this, we are gonna create a `models.py` but you can write these in your `app.py` as well.  

```python
from flask_login import UserMixin, LoginManager, login_required, login_user, logout_user, current_user
from flask_mongoengine import MongoEngine, DoesNotExist
from werkzeug.security import generate_password_hash, check_password_hash
from settings import db
import datetime

# Database Modals
class User(UserMixin, db.Document):
	created_at = db.DateTimeField(default=datetime.datetime.now, required=True)
	username = db.StringField(max_length=255, required=True)
	password = db.StringField(max_length=255, required=True)

	def __unicode__(self):
		return self.id

	def set_password(self, password):
		self.password = generate_password_hash(password)

	def is_authenticated(self):
		return True

	def is_active(self):
		return True

	def is_anonymous(self):
		return False

	def check_password(self, password):
		return check_password_hash(self.password_hash, password)

	@staticmethod
	def validate_login(password_hash, password):
		return check_password_hash(password_hash, password)

	meta = {
		'allow_inheritance': True,
		'indexes': ['-created_at', 'username'],
		'ordering': ['-created_at']
	}
```

Now comes an issue, our global variables inside app cannot as such be used in models, but then we don't need duplications, so we will now shift those global variables to another `settings.py` file and import it wherever we need:  

```python
from flask_mongoengine import MongoEngine
from flask import Flask

global db
global app
app = Flask(__name__)
db = MongoEngine()
app.config['MONGODB_DB'] = 'TODO'
app.config['MONGODB_HOST'] = 'localhost'
app.config['MONGODB_PORT'] = 27017
app.config['MONGODB_USERNAME'] = ''
app.config['MONGODB_PASSWORD'] = ''
app.secret_key = "your secret key for the application goes here"
db.init_app(app)
```
Here you can specify any database auth if you have enabled them for your MongoDB. Alright, let's move on. Let's add the backend API for `/login` `POST` request that we wrote in our angular controller function a while ago. So, in your `app.py`, make these changes.  

```python
from flask import Flask, jsonify, render_template, url_for, request
from flask_login import UserMixin, LoginManager, login_required, login_user, logout_user, current_user
from flask_mongoengine import MongoEngine, DoesNotExist
from settings import db,app
from models import User
import bcrypt

# now get all the models from models.py ( this can be inside app.py)
# am just making it in models.py for readbility

login_manager = LoginManager()
login_manager.init_app(app)


@login_manager.user_loader
def load_user(user):
	return User.objects.get(id=user)

# ------------------------------------------------------------------------------------
# 			Flask Login ends
# ------------------------------------------------------------------------------------
@app.route("/")
def index():
	return render_template('index.html')

@app.route("/login", methods=['POST'])
def login():
	params = request.get_json()
	print params
	try:
		user = User.objects.get(username=params['username'])
		if user.validate_login(user.password, params['password']):
			user_obj = User.objects.get(id=user.id)
			login_user(user_obj)
			return jsonify({'status': True})
	except DoesNotExist:
		return jsonify({'status': False})
	return jsonify({'status': False})

if __name__ == '__main__':
	app.run(debug=True, threaded=True, host='0.0.0.0')
```
Here, we first take the data/params from the post request, that is sent from client as JSON. So, we use `get_json()` to get them. Also, notice, that we have restricted the request method to only `POST` in the `@app.route("/login", methods=['POST'])`. While you login the user, we need to validate the credentials with the stored (hashed) passwords in the database (You will see this soon). So, if the credentials match, we `login_user(user_obj)`, else we send a Auth Fail message.  

Now, we need to complete the Signup end, so that we can test both. So, add these to html,js,app.py files respectively,  

```html
{% raw %}<!-- Signup Modal -->
<div class="modal fade" id="signupModal" role="dialog">
	<div class="modal-dialog" >
		<div class="modal-content">
			<div class="modal-body">
				<form ng-submit="signup()">
				<div class="form-group">
					<label class="text-success">Username: </label>&nbsp;<input class="form-control" type="text" name="username" placeholder="Case insensitive">
				</div>
				<div class="form-group">
					<label class="text-success">Password: </label>&nbsp;<input class="form-control" type="password" name="password">
				</div>
				<div class="form-group">
					<input class="btn btn-success" type="submit" name="signup" value="Signup">
				</div>	
				</form>			
			</div>
		</div>
	</div>
</div>
{% endraw %}
```

Now, add the controller functions to make http request to the APIs we write for signup  

```javascript
tApp.controller('TodoController', ['$scope', '$http', '$window', '$route', function($scope, $http, $window, $route) {
	// other functions above

	$scope.signup = function(){
		$http({
			url: '/signup',
			method: 'POST',
			data: JSON.stringify({
				'username':$scope.signup.username,
				'password':$scope.signup.password
			}),
			headers: {'Content-Type':'application/json'}
		}).then(function(response){
			console.log(response['status'] + ' ' + response['statusText']);
		});
	}
}]);
```
And, finally finish the Signup API at the backend this way.  

```python
@app.route("/signup", methods=['POST'])
def signup():
	params = request.get_json()
	print params
	try:
		print 'try'
		data = User.objects.get(username=params['username'])
		return jsonify({'status': False})
	except DoesNotExist:
		print 'except'
		hashed_pass = bcrypt.hashpw(str(params['password']), bcrypt.gensalt())
		user_obj = User(username=params['username'])
		user_obj.set_password(params['password'])
		user_obj.save()
		return jsonify({'status':True})
```

This, way we are gonna add the Logout option and some modifications on what to show initially (when not logged in) and then the tasks after the user Logs himself in. So you can see these changes in the commits [here](https://github.com/sreecodeslayer/todo-flask/commit/e794ddbc2bb0b047cd3af38af03241067c30cb25).
Now, we can start the actual API for to-do app, i.e. making CRUD routes to which the user can send request to create/read/update/delete tasks!  

To do this, we first need to make the modals for the Task, like we did for creating User accounts. So let's do that now. Switch to the `models.py` and add following code given below:  

```python
class Task(db.Document):
	created_at = db.DateTimeField(default=datetime.datetime.now, required=True)
	user = db.ReferenceField(User)
	task_id = db.StringField(max_length=255, required=True)
	task_title = db.StringField(max_length=50, required=True)
	task_content = db.StringField(max_length=255, required=True)
```

Like in any ORM, MongoEngine can help us handle each user's tasks separately much like the foreign key concept in SQL Databases. Here they are called as Reference Fields. The `user` reference field maps each entry to the Collection with the `current_user.id` using the Flask-Login module. This way, it becomes easy for us to get the tasks exclusively for that specific user, from the tasks collection having all task (by all users).  

Once the model class is defined, we can start using them to do our CRUD operations, in the following way:  
#### First, we need to import this model as well from the `models.py` along with `User` which we did before.  
So change  

```python
from models import User
```  
in `app.py` to  

```python
from models import  User, Task
```  
also, if we think of the tasks that we store in the collection, we will have to identify each task separately so that they can be CRUDed, for this, we need some kind `Primary key`. Obviously, MongoDB uses `_id` for each `document` in the collection. But we really don't want to show those off to user as it will be kind of ugly. (duh)  
So then, let's look at `uuid` which almost everyone nowadays uses for uniqueness and that avoid collisions. For that we need to import another Python package, viz.,  

```python
from uuid import uuid4
```  
And, now add the CRUD operations under Flask app routes as follows:  


```python
# -----------------------------------------------------
# 		TASK CRUDs
# -----------------------------------------------------
# CREATE
@app.route("/new", methods=['POST'])
@login_required
def new_task():
	data = request.get_json()
	# Add new task for the user
	print data
	try:
		task_obj = Task()
		task_obj.user = User.objects.get(id=current_user.id)
		task_obj.task_title = data['task_title']
		task_obj.task_content = data['task_content']
		task_obj.task_id = str(uuid4())
		task_obj.save()

		# Now get all task for this user and return it
		all_tasks = Task.objects(user = current_user.id)
		his_tasks = []
		for task in all_tasks:
			his_tasks.append({
				'task_title':task.task_title,
				'task_content':task.task_content,
				'task_id':task.task_id
				})
		return jsonify({'status':True,'tasks':his_tasks})
	except Exception as e:
		print e
		return jsonify({'status':False})
```
Here, we can see, we have taken the params from the user (task title,content), and added that to the Collection with his `User` Reference and a unique ID for the task using our `uuid4`, and then collect all tasks together (this will include the newest one that he/she adds) and send it back to the `view` as JSON, and update it on the other side.  
Similarly we will add the `Update` operation.

```python
# UPDATE
@app.route("/edit", methods=['POST'])
@login_required
def edit_task():
	data = request.get_json()
	# Edit the particular task
	print data
	try:
		task_obj = Task.objects.get(user = current_user.id, task_id = data['task_id'])
		task_obj.task_content = data['task_content']
		task_obj.task_title = data['task_title']
		task_obj.save()

		# Now get all task for this user and return it
		all_tasks = Task.objects(user = current_user.id)
		his_tasks = []
		for task in all_tasks:
			his_tasks.append({
				'task_title':task.task_title,
				'task_content':task.task_content,
				'task_id':task.task_id
				})
		return jsonify({'status':True,'tasks':his_tasks})
	except DoesNotExist as e:
		print e
		return jsonify({'status':False})
```  
Here again, if you look at the code, there is only little difference from adding a new one. In this case, we firstly want to select that particular task which the user has asked to be edited. So, for this, we will make use of the `uuid4` id, which we assigned in `/new` route, to select that task, edit the document and save it. After this, we will again collect all the tasks for that user (this will include the one that he/she just edited with the changes), and send it back to the `view` as JSON, and update it on the other side.  

Likewise, let's finish off the `Delete` operation and a small route to load all tasks wherever we need.  

```python
# DELETE
# DELETE
@app.route("/delete", methods=['POST'])
@login_required
def delete_task():
	data = request.get_json()
	# Delete the particular task
	print data
	try:
		Task.objects.get(user = current_user.id, task_id = data['task_id']).delete()

		# Now get all task for this user and return it
		all_tasks = Task.objects(user = current_user.id)
		his_tasks = []
		for task in all_tasks:
			his_tasks.append({
				'task_title':task.task_title,
				'task_content':task.task_content,
				'task_id':task.task_id
				})
		return jsonify({'status':True,'tasks':his_tasks})
	except DoesNotExist as e:
		print e
		return jsonify({'status':False})

# ALL TASKS
@app.route("/tasks", methods=['GET'])
@login_required
def get_tasks():
	data = request.get_json()
	# Return all the tasks created by the {% raw %}<user>{% endraw %}
	return jsonify({'status':True,'tasks':tasks})

```

> One thing to note here is that, while I explain the backend codes, simultaneously, the data that gets returned to the `view` needs to be populated back to the `user` webpage, which has already been covered in the `javascript` snippets. If you come across any doubts, feel free to use the comment section.  

> This blog post is a bit late compared to the time I pushed the codes to the actual project repository, so there can be changes in the AngularJs side, which I haven't yet updated in the repo. I understand if some of you are frustrated with an outdated codebase, but I haven't had much of the time to go through the changes I need to make (if any). Please feel to contribute to the project, go ahead and make some pull requests, I'll surely go through them.

> This tutorial is basically aimed at guys who want to learn about RESTful API and Python Flask, and doesn't deal with more complex security aspects and all of those sorts which comes later, once you master the framework and basics of REST.

As a sidenote, I would like to recommend using a REST API client, I suggest using [insomnia.rest](https://insomnia.rest/) and for administrating your MongoDB collections, you could try [GenghisApp](http://genghisapp.com/) or MongoCheff, now known as [Studio3t](https://studio3t.com/).  
<iframe src="http://ghbtns.com/github-btn.html?user=sreecodeslayer&type=follow&count=true" allowtransparency="true" frameborder="0" scrolling="0" width="100%" height="10%"></iframe>