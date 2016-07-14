---
layout: post
permalink: /web-workers
title:  Service Workers
categories: javascript
tags: javascript service-workers
published: true
---

Maybe you heard about progressive web applications, service workers,
offline websites and stuff like that.

Future is coming and the Service Workers will for sure lead a big role
on that but, unfortunately, there is still a lot of confusion around.

So, my goal for this article is to provide you with a clear idea about what
a service worker is; what is not; why we will inevitably start using
them sooner or later and, then, how to start using a Service Worker right now.

## What a Service Worker is

A Service Worker AKA `sw.js` is just a JavaScript file running in your website
background.

So, what's the difference between a Service Worker and a normal JavaScript file
you import in your HTML document?

Well, in theory there is absolutely no difference at all. A Service Worker
is and will alway be just a JavaScript file.
However the way Browsers interact with a Service Worker is completely
revolutionary.

When you define a Service Worker in your app, your browser loads and keeps
that script in the Browser's background ready to be used by the app at any time
and. Once loaded, it will be always available even without the presence of an
Internet connection.

Ok, before we continue. Just have a quick look at:

## What a Service Worker is not

Service Workers are not used for storing frameworks or libraries like Angular
or jQuery, also they are completely DOM blinded which means that they cannot
directly interact with your DOM.

Trying to access the `document` object inside a Service Worker
will result in an error because that `document` will just return `undefined`.

Ok, now you're probably confused because we said we can use Service Workers for
running JavaScript code in the background and they can work without Internet
connection, however they cannot interact with the DOM so we cannot simply
minify all our JavaScript code inside a `sw.js` file.

**So, what's the point?**

The power behind a Service Worker is another. In fact they can have access to
your browser's cache storage meaning that they are capable of storing all your
files inside the browser and serve your application with them in order to
increasing the rendering process and also, to give your app an offline support.

## HTTPS only

Before we proceed is important to highlight the biggest restriction on
Service Workers.

Because this service runs in your browser's background, using Service Workers
from unsafe websites can be extremely dangerous as they can hijack connections,
respond differently, & filter responses.

To avoid man-in-the-middle attacks, you can only register for Service Workers on
pages served over HTTPS, so we know the Service Worker the browser receives
hasn't been tampered with during its journey through the network.

Github Pages like Heroku are served over HTTPS,
so they're a great place to host demos.

Also, you can still use Service Workers on your development environment
as they accept to be served from `localhost` without any extra configuration.

## Let's build our first Service Worker step-by-step

Ok then, we spoke a lot about them but we haven't seen any single line of
code so far.
So let's begin defining the Service Workers lifecycle.

### Registration

In order to start using a Service Worker, you need first to register your script
as a Service Worker because, as we said before, this is just a normal
JavaScript file so your browser is not able to automatically identify a
Service Worker by itself.

To tell the browser where your Service Worker JavaScript file lives, you need to
use the navigator service. inside your `index.html` body file, create
the following script:

```html
<script>navigator.serviceWorker.register('/sw.js')</script>
```

However, there is a proper installation method you should follow in order to
handle errors and provide a proper cross-browser support.

A bullet-proof register process is just a wrapper around the previous
registration one but is alway best practice to follow a clear path in order
to avoid errors.

So here is a standard Service Worker registration process.

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker
    .register('/sw.js')
    .then(function(registration) {
      // Registration was successful
      console.log('ServiceWorker registered with scope: ', registration.scope);
    })
    .catch(function(err) {
      // registration failed :(
      console.log('ServiceWorker registration failed: ', err);
    });
}
```

Right, but why we need all those lines of code?

First of all, Service Workers are still not supported by all the browsers so
we need to make sure that our final user's browser knows what a
Service Worker is.

This is covered in very first line of code:

```js
if ('serviceWorker' in navigator) {
```

You will then probably want to include a polyfill to make sure all the browser
can recognize this service. We are not going to cover that part in here but
you can have access to all the resources you need from this [link][sw-resources]

Let's move to the next line. Service Workers make a massive use of promises
(if you are not confident with this concept, we highly recommend you to
  have a look at [this article][promises-link] before proceeding).

```js
navigator.serviceWorker
  .register('/sw.js')
  .then(function(registration) {
    // Registration was successful
    console.log('ServiceWorker registered with scope: ', registration.scope);
  })
```

As soon as the registration process success, a Service Worker will available
a scope referring by default to your website address. So, if you launch
this code from your localhost environment you will see in the console
message that your Service Worker scope will be in fact `localhost`.

If you want to specify a different scope for your Sevice Worker, you can
manually pass a scope property during the registration method in this way:

```js
navigator.serviceWorker
  .register('/worker.js', {
    scope: '/my-scope/'
  })
  .then(function(registration) {
    // Registration was successful
    console.log('ServiceWorker registered with scope: ', registration.scope);
  }, function(err) {
    // registration failed :(
    console.log('ServiceWorker registration failed: ', err);
  });
```

So, this time, your Service Worker scope will reside inside `localhost/my-scope`

If we register the service worker file at /my-scope/sw.js,
then the service worker would only see fetch events for pages whose URL starts
with /my-scope/ (i.e. localhost/my-scope/page1/, mywebsite.com/my-scope/page2/).

### Installation

For the installation process you need to create a `sw.js` file on the same
folder where your `index.html` file resides. Then we are ready to write
our first event listener inside:

```js
self.addEventListener('install', function(event) {
  console.log('Service Worker Installed!');
});
```

This will just being called during the previous registration process.
What we need to do now is creating a caching bucket where storing all the files
we want to store for an offline access.

We can then define an array of files we want our Service Worker to cache
alongside the installation process:

```js
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/scripts/app.js',
  '/images/background.png'
];
```

and pass inside the inside installation function like this:

```js
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches
      .open('myCache')
      .then(function(cache) {
        return cache.addAll(urlsToCache);
      })
  );
});
```

This code will create a new cache object named `myCache` and will store all
the files listed in the `urlsToCache` array.

### Fetch

Now that we've installed a Service Worker and instructed to store a bunch
of files inside the browser's cache, it's time for returning back these files.

After a service worker is installed and the user navigates to a different
page or refreshes, the service worker will begin to receive fetch events.
Basically, our Service Worker will act like a Proxy to intercept any HTTP
request our app will make.

So, we can simply intercept those `fetch` calls and immediately respond with
our cached files where we have one already.

Let's start with defining another eventListener inside our sw.js file then:

```js
self.addEventListener('fetch', function(event) {

});
```

Now we need to identify the name of the file the application is trying to reach
and, in case we already have that in our cache, send that back instead of
performing a request outside.

```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches
      .match(event.request)
      .then(function(response) {
        return response || fetch(e.request);
      });
  );
});
```

`caches.match()` evaluates the web request that triggered the fetch event,
and checks to see if it’s available in the cache.
It then either responds with the cached version, or uses fetch to get a copy
from the network.
Finally, the response is passed back to the web page with `event.respondWith()`.


## Demo project

A GitHub repository showing how to create a Service Worker in a real
project is available for you at [this link][github-link].

I really hope you found this article helpful and I'm looking forward to
receiving your feedbacks!

[sw-resources]: https://jakearchibald.github.io/isserviceworkerready/resources.html
[promises-link]: http://www.html5rocks.com/en/tutorials/es6/promises/
[github-link]: https://github.com/andreasonny83/service-workers
