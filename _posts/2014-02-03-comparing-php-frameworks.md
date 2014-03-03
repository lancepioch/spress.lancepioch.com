---
layout: post
categories: [php]
tags: [php, frameworks, compare, comparison, best, worst, practices, reasons]
class: php
---

## Reasons For

### Open Source

Most of the code in your project will be from the framework itself. This is true even if the project you're doing is proprietary code. The fact is, you get the benefit of having hundreds or thousands of people already testing and using this code to its limit.

### Updates

Because the framework is open source, updates are released continuously with security and bug fixes. Updating to the latest changes usually takes no more than a `composer update` and possibly a few lines of code having to be changed.

### Your Mistakes

While you'll probably only make the same number of mistakes as everybody else usually does, you won't have their help with maintaining your application, unless it is open source **and** popular.

### Security

Changes are, you have an application that requires a user to login, or to authenticate themselves in some way. At the very least, you'll be accepting inputs from your mysterious average user. Copying your own code or other's code to do this each time can result in glaring holes of security. Sometimes it's better to use the libraries that already sanitize inputs for you, some automatically too.

## Reasons Against

### Speed

Unless you're utilizing [Phalcon](http://phalconphp.com), then most likely you will see noticeable performance hits of as low as 200ms to as high as 2000ms. Each framework has its own benchmarks and speed comparisons, but sometimes it really does matter if have the need for speed. Serving up pages faster doesn't just mean benefits for your users, but it also means that your web server isn't spending as much time with other users as it could be.

![Peformance Benchmark of PHP Frameworks][1]

If you're running a processing intensive application or do not have a majority of users, then the extra hundred milliseconds isn't going to harm anyone. However if you have hundreds of people every several minutes, then it could cut your bill in half if you're using a server with a very expensive processor.

### Others' Mistakes

Whether you like it or not, you'll always be using someone else's code, guaranteed or your money back. But whether or not you are a beginner or an expert of 30 years, there's always mistakes to be made, by you and everyone else.


  [1]: http://i.imgur.com/U5VxlOi.png