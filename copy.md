# bool.js
_Insanely easy, way cool and f\*\*\*ing awesome. It's bool.js_

## What is bool.js?

Bool.js started as an architecture to achieve ease at creating new projects, and now, is more than this. Bool.js lets you create APIs in no time, using elements you already know or can easily learn. We invested our experience, love and countless days to give you the best development experience, so you don't have to do it twice.

Less kitten samples and more fun, it's bool.js.

## Prerequisites

* **Node.js v4.0.0 or later**: No, you can't escape from this, as our incredibly modern API uses the latest features of the ES6 standard, such as classes and that stuff.
* **Git 1.9.0 or later**: Recommended for having control in every aspect of your project as it rapidly grows.

## Let's begin

> _TL;DR_
>
> You can do it in two ways: the hard one or the easy one. The first implies cloning our boilerplate, taking your time to deeply know the workspace and begin the project by .

### The hard one

Start by cloning the boilerplate,

```bash
git clone https://github.com/booljs/booljs-boilerplate.git sample
```

Once you get it, open the folder where it is located and delete last

```bash
cd sample
rm -rf .git
git init
```

Then, initialize the project and install bool.js

```bash
npm init
npm install -S bool.js
```

Finally, modify line 2 in `index.js`, should look like this:
```js
var booljs = require('bool.js');
```

And fill out the base namespace of your application (yes, we use namespaces, I'll tell you about this later) in line 5. For example, if your API is being located in http://api.awesomeapp.com, your namespace would be something like:
```js
booljs('com.awesomeapp.api').run();
```

`index.js` will end up this way:

```js
'use strict';
var booljs = require('bool.js');

// Here is where magic happens
booljs('com.awesomeapp.api').run();
```

Now, you're ready to start.

### The easy one

Simple as this: install bool.js in your computer through npm.

```bash
sudo npm install -g bool.js
```

Then, use the CLI to start the application. Fill out the namespace (yes, we use namespaces, I'll tell you about this later) and the directory where your project is being created. In case you don't mention a path, CLI will use the one you're being located.

```bash
bool create <namespace> [dir]
```

Follow the instructions and you're done.

## First run

> Basic code comes with an example of: configuration files, found in… configuration (D'Oh!) folder, controllers, DAO (or business logic, if you think in a better name than ours, let us know [here](mailto:hello@booljs.co)), models, and plugins.

We prepared a _kitten free_ sample using a dog store, where you can see how to create a resource. Just type `node index.js` and you're in.

## Dissecting workspace structure

> TL;DR
>
> Bool.js code are plain functions with at least one parameter: `app`, which is a reference to the namespace-based components, from where you can instantiate your code. Those return the objects you can use everywhere in your application. They can be controllers, DAO, models, routes and plugins.
>
> Models and routes are very strict in their structure, as they're the interfaces to connect the rest of the architecture and follow the specifications of the database and web server drivers. The others (unless specified) are designed up to your criteria, following certain code styles and conventions.
>
> Finally, depending on how you name your files, they will be referenced as CamelCase classes in the namespace structure.

### Namespaces

If javascript is not your first programming language, you'll probably know the concept of namespaces. I mean, they exist almost everywhere: Python, Java, C++/C#… even PHP use them, altough you didn't know it. However, if you are not familiar to them, here's the best introduction you'll ever find about what a namespace is.

> Namespaces are a hierarchical way to arrange code, dividing it into... what? You said it'd be easy. Let's start again.

Have you ever used sorted your music in folders? First you sort them by artist, them by albums and finally (if needed) by CD numbers. Then, you look up your favorite songs easier than having all them merged in one place, in a way like: `artist/album/cd1/my_favourite_song.mp3`.

Well, that kind of sorting can be made in code too! We call it namespaces, and are simple as this: sort code into similar kinds of behaviours, so if you'd like to know where is the (for example): Dog's controller, you'll find it into the controllers module, into the application. Then, look up in a way like this: `application.controllers.Dog`. Pretty similar, huh?

In bool.js there are seven essential kinds of components: configuration files, controllers, business logic code (better known as DAO), models, routes, plugins and views.

#### File conventions

### Modules: what are them?

A module is basically a function containing executable code, which passes parameters referencing the application's other components through namespaces, as well as other parameters: database drivers, libraries, etc. You can recognize them as code files into the following folders: `controllers`, `dao`, `plugins`, `models` and `routes`.

They consist in three parts: the header definitions, the code body and the returning section. Now, let's examine very carefully each one of these.

#### Header section

This is the part of code defining what is going to be exported as a component. Consists on a `use strict` header, because we like to be strict, but safe; and a `module.exports` sentence returning a function. The function takes at least one parameter: can be called whatever you want, but we recommend to call it `app`, so you will know it is about the application's status.

```js
'use strict';

module.exports = function (app) {

};
```

#### Definitions section

Essentially is the part where you define your variables. Here you are free to do whatever you want, with some recommendations.

1. Don't use `require`. The reason to make bool.js namespace-based is precisely to avoid making complicated calls to files. If you have a recurring piece of code you want to use, then create a module of it in the `plugins` folder, and then you can call it instantiating the component `app.plugins.WhateverYouCanCallItPlugin`.
2. Never declare variables or running code outside code body, before or after the function definition. This is an anti-pattern as can turn your code non-stateless, and eventually cause troubles.
3. When you are importing application components, remember to point constructors: then, in the code body, instantiate them, so you'll mantain your code stateless.

A good example of achieving this, is the definition section of a controller.

```js
'use strict';

module.exports = function (app) {
    var Dog     = app.dao.Dog
    ,   Json    = app.views.Json
    ,   Uuid    = app.plugins.Uuid;

}
```

#### Code body

Here comes the fun part. The code body is exactly that: an object (array, function, whatever), where you put all your skills and imagination to make it work. Generally, is a literal object (yes, a moustache `{}` containing a bunch of functions and fields) where executable code is located, and is returned by the function to be used in other parts of the application. Depending on the component type, you must follow some interfaces for these functions, like controllers, but this doesn't always happen.

Here is an example of a code body, from the controller:

```js
return  {
    list: function (req, res, next) {
        var dog     = new Dog()
        ,   json    = new Json();

        json.promise(dog.list(req.query), res, next);
    },
    create: function (req, res, next) {
        var dog     = new Dog()
        ,   json    = new Json();

        json.promise(dog.create(req.body), res, next);
    },
    update: function (req, res, next) {
        var dog     = new Dog()
        ,   json    = new Json();

        dog.update(req.params.id, req.body, function (err, data) {
            if(err) return next(err);
            json.standard(data);
        });
    }
};
```

Altough we use to return functions in a literal object, a good (and sometimes cleaner) choice might be returning a reference to `this` and declaring functions as fields of the function. This is something like the following example.

```js
this.list = function (req, res, next) {
    var dog     = new Dog()
    ,   json    = new Json();

    json.promise(dog.list(req.query), res, next);
};

this.create = function (req, res, next) {
    var dog     = new Dog()
    ,   json    = new Json();

    json.promise(dog.create(req.body), res, next);
};

this.update = function (req, res, next) {
    var dog     = new Dog()
    ,   json    = new Json();

    dog.update(req.params.id, req.body, function (err, data) {
        if(err) return next(err);
        json.standard(data);
    });
};

return this;
```

This way is often used in mongoose models, because we are returning an Schema instance, and into it, we declare handy functions and mongoose plugins.

### The router stuff

**Router components** are _modules_ that contain an array of routes that must be deployed by the web server driver. A route
