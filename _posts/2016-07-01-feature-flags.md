---
layout: post
permalink: /feature-flags
title:  Feature flags
categories: javascript
tags: javascript chrome
comments : true
---

How many of you have ever wanted to have "secret" features released into their
production or staging websites?

Well I'm one of them and, if you're looking forward to do something similar,
I've already released a Chrome extension to make your life easier.

First of all, feature flag is not a new concept. Developers have used a similar
concept for ages for releasing hidden features in their applications and Google
Chrome like FireFox has an dedicated configuration page for easily
enable/disable these features.

So, first things first, [here the Chrome extension][feature-flag-extension]
ready to be installed and completely free ;)

![Imgur][feature-flags-screenshot]

Now, why we want to have secret features in our websites? Well, the reasons
could be really various. The first one that pop in my mind is because we want
to test something is still in an experimental stage or we simply want to demo a
new feature to someone external to our company so we need to find a way for
them to have access to that implementation.

The reason I developed this Chrome extension was because of my company requirement. I was working in a front-end development team where we used to work on clients implementation before the actual back-end was ready to support them. The strategy we adopted in order to achieve this porpoise was to mock the API in our client application and reproduce the missing back-end features. This was fine on a development environment. However we wanted to continuos deploy our code instead of leaving that sitting on a feature branch for weeks, so we introduced the feature flag concept.
The idea was really easy: we deploy a feature but we hide that behind a feature flag. We decided to use a "features" cookies containing a list of tags to easily turn those features on and off. However, as you can probably imagine, product owners, web designers, etc were not really confident with the idea of hacking a website creating cookies. So that's why I had the idea of creating a Chrome Extension for that.

The plugin is nothing crazy and the code is available on GitHub. You just need to write the feature name you want to enable in your website and press "return". The Chrome extension just create a features cookie and set the value to the feature you wrote in the input field, then triggers a page refresh to render the new feature into the website.
The Feature flags extension comes with a demo page out of the box where you can see this concept in a real scenario.

Please, feel free to write comments and suggestions on this post. :)

[feature-flag-extension]:https://chrome.google.com/webstore/detail/chrome-feature-flag/ekipaajelfmafbclnihjedhmelofhecl
[feature-flags-screenshot]:http://i.imgur.com/qSIgNRB.jpg
