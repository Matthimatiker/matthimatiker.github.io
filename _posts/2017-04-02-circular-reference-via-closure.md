---
layout: post
title: "You Can Always Refcount On Me: Avoiding Circular References When Storing Closures in Attributes"
---

**TL;DR**: _When creating a closure in an object context and storing it in an attribute, then you create a circular reference._

### Problem Context ###

[Closures](http://php.net/manual/en/functions.anonymous.php), also known as anonymous functions, are a convenient feature that is available since PHP 5.3:

    $rounded = array_map(function ($number) {
        return round($number);
    }, $numbers);

As of PHP 5.4 it is also possible to access ``$this``, if the closure was created in an object context:

    class NumberMapper {
        public function createMapper() {
            return function ($number) {
                return $this->myCreativeRound($number);
            };
        }
    
        private function myCreativeRound($number) {
            return round($number, 3);
        }
    }
    
However, if you store such a closure in an object attribute, then you have created a circular reference:

    class NumberMapper {
        private $myMapper;
        
        public function __construct() {
            $this->myMapper = function ($number) {
                return round($number, 3);
            };
        }
        
        public function createMapper() {
            return $this->myMapper;
        }
    }

In the example above, an instance of ``NumberMapper`` holds a reference to the closure (via attribute ``$myMapper``) and the closure holds a reference to to the object instance, as it was created in the object context.  
The reference from closure to object is not that easy to spot and if you do not access the context via ``$this`` from within the closure, you might have created that reference accidentally.

This circular reference has the (mostly invisible) effect of preventing early destruction of ``NumberMapper`` instances as their reference count ([refcount](http://php.net/manual/en/features.gc.refcounting-basics.php)) never reaches zero. These instances will occupy memory until the PHP process terminates or circular garbage collection is triggered.

### Solution ###

To avoid that reference, you can remove the context from the closure via ``bindTo()``:

    public function __construct() {
        $this->myMapper = (function ($number) {
            // [...]
        })->bindTo(null);
    }

Please note that ``bindTo()`` does not modify the closure it is called on, but creates a new one instead.  
Alternatively, you can directly create a static closure without context:

    public function __construct() {
        $this->myMapper = static function ($number) {
            // [...]
        };
    }

### Conclusion ###

When creating a closure, actively decide if you need a reference to the object context  and adjust your closure construction accordingly.
