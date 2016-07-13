---
layout: post
permalink: /definitive-module-pattern
title:  The Definitive Module Pattern
categories: javascript
tags: javascript
published: false
---

I know, another article about the module patter in JavaScript.
I'm perfectly aware of the fact that there are millions of similar posts
out there so my intention is not to be original but to help people to
become more confident with Javascript.

# Why will you find this article useful


## Starting from the beginning

### The IIFE - Immediately Invoked Function Expression

```js
(function(global) {
  'use strict';

})(window || this);
```
