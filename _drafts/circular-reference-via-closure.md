---
title: "You Can Always Refcount On Me: Avoiding Circular References When Storing Closures in Attributes"
---

**TL;DR**: _When creating a closure in an object context and storing it in an attribute, then you create a circular reference._

[Closures](http://php.net/manual/en/functions.anonymous.php), also known as anonymous functions, are a convenient feature that is available since PHP 5.3.

PHP
refcount
context 
replace by null, bindTo() return value
