---
layout: post
title:  "Learn how to combine Python-Flask with VueJS-Webpack"
date:   2018-01-31
excerpt: "A simple post to learn setting up Python-Flask framework, Webpack and VueJS."
tag:
- python 
- flask
- vuejs
- webpack
- vue-cli
comments: true
---
So, unlike my previous posts, this one will focus mainly on how you can setup a Flask serving a VueJS application packaged using Webpack. When I initially started off with VueJS, I liked the idea of using it from CDN and not worry about packaging using yarn or npm and webpack. I really hated those. In fact this post is also my regret of not trying to understand these build tools.  

Let's look at how we can combine VueJS + Webpack with Flask application, and finally serve the web files using the Flask server, ie. without using a Node server or another frontend server.

Firstly, head over to [Webpack Template for VueJS](https://vuejs-templates.github.io/webpack/) or read on here on how to setup a VueJS project [errr... Python Flask application]

* Install the official CLI Tool for VueJS `vue-cli` (One time)  

```bash
$ npm install -g vue-cli
```
OR

```bash
$ yarn global add vue-cli
```

* Head over to the directory you want your project to be on your shell.
* Initialise the project root directory with:

```bash
$ vue init webpack vue_flask
```
* Head over to the `vue_flask` folder.

```bash
$ cd vue_flask
```  
* Install the dependencies that the vue-cli has already configure for you.

```bash
$ npm install
```
OR

```bash
$ yarn install
```

* Now you can basically start your dev server with npm or yarn.

```
npm run dev
```
OR

```
yarn run dev
```

So, that is, basically how you can setup a dev environment for Frontend Developemnt. But when you go into Production, you need to build the files into the Flask application package for it to serve them easily.

Let's see how this can be done.
In the `vue_flask` folder, you can see a bunch of folders, our current focus is towards the folder that says, `config` which is the webpack configuration. Inside this folder, there should be a dev, prod,(and test - if enabled) and an `index.js` file. Open the `index.js` file, and find the lines that says:

```javascript

build: {
	// Template for index.html
	index: path.resolve(__dirname, '../dist/index.html'),

	// Paths
	assetsRoot: path.resolve(__dirname, '../dist'),
	assetsSubDirectory: 'static',
	assetsPublicPath: '/',
	.
	.
	.

```
If you take a look at the line that says, `assetsRoot`, it currently points to `dist` folder that is suppose to reside inside the root folder, in our case, `vue_flask`. This needs to change into our Flask applciation. So make the changes accordingly for assets and index.
Let's conside our flask application resides inside the folder called, `flask_server`, (ie. `vue_flask/flask_server` is where the assetsRoot should be)

so, the lines become:

```javascript
build: {
	// Template for index.html
	index: path.resolve(__dirname, '../flask_server/templates/index.html'),

	// Paths
	assetsRoot: path.resolve(__dirname, '../flask_server'),
	.
	.
	.
}
```
As long as your Flask static files are to be placed under `static` folder and your template files under `templates`, the changes made above is all you need for everything to be up and running.
If you want to take a look at how I have setup the same for WIP application, head over [here](https://github.com/sreecodeslayer/100-Days-of-Python/tree/master/035/ecommerce)

And with that you have easily setup a Flask Application with Native webpack support for VueJS.

The reason why you might have to end up using Browserify or Webpack, like I had to, is because, almost all the Vue Plugins and Extensions are focused on these and are distributed as npm packages instead of our traditional CDN. I was frustrated to not get the CDN versions of some of the plugins that I needed while I was starting out.

If you have doubts on the direcory structure, take a look at the tree below.

```
./
├── flask_server
│   ├── app.py
│   ├── static
│   │   ├── css
│   │   │   ├── app.fbdd172291717f61eab68abdca07e422.css
│   │   │   ├── app.fbdd172291717f61eab68abdca07e422.css.map
│   │   │   ├── materialize.css
│   │   │   └── style.css
│   │   ├── fonts
│   │   │   ├── MaterialIcons-Regular.eot
│   │   │   ├── MaterialIcons-Regular.ttf
│   │   │   ├── MaterialIcons-Regular.woff
│   │   │   └── MaterialIcons-Regular.woff2
│   │   ├── images
│   │   │   └── gift-brown-shopping-market.jpg
│   │   └── js
│   │       ├── app.1721ad433c2271ef202e.js
│   │       ├── app.1721ad433c2271ef202e.js.map
│   │       ├── manifest.2cc46eb7f42ac0c00c33.js
│   │       ├── manifest.2cc46eb7f42ac0c00c33.js.map
│   │       ├── vendor.e7480045de790a6256ab.js
│   │       └── vendor.e7480045de790a6256ab.js.map
│   └── templates
│       ├── index.html
│       └── login.html
├── build
│   ├── build.js
│   ├── check-versions.js
│   ├── logo.png
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   └── webpack.prod.conf.js
├── config
│   ├── dev.env.js
│   ├── index.js
│   └── prod.env.js
├── index.html
├── package.json
├── README.md
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   ├── AboutUS.vue
│   │   ├── AllCategory.vue
│   │   ├── ContactUs.vue
│   │   ├── ECommerceHome.vue
│   │   ├── Electronics.vue
│   │   ├── Fashion.vue
│   │   ├── HomeKitchen.vue
│   │   └── Sidebar.vue
│   ├── main.js
│   └── router
│       └── index.js
├── static
│   ├── css
│   │   ├── materialize.css
│   │   └── style.css
│   ├── fonts
│   │   ├── MaterialIcons-Regular.eot
│   │   ├── MaterialIcons-Regular.ttf
│   │   ├── MaterialIcons-Regular.woff
│   │   └── MaterialIcons-Regular.woff2
│   └── images
│       └── gift-brown-shopping-market.jpg


```