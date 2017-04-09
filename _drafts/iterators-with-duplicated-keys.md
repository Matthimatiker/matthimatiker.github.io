---
title: "Lost In Iteration"
---

**TL;DR**: When using ``iterator_to_array()`` with default parameters, some extracted elements are lost if they share the same key. 

### Problem Context ###

When writing tests I often find myself using ``iterator_to_array()`` to quickly inspect the items contained in a traversable object:

    $items = iterator_to_array($myTraversableObject);

Converting an object into an array this way allows me to rely on the huge amount of PHP's array functions for further checks.

However, there is a catch when using ``iterator_to_array()`` without its second argument: Usually your are only interested in the elements, but ``iterator_to_array()`` keeps the keys of the ``\Traversable`` per default. And it is perfectly fine when a ``\Traversable`` uses the same key multiple times.  
Wonder how this can happen (accidentally)? Have a look at this simple example:
 
    $firstPart = new \ArrayIterator(array('a', 'b', 'c'));
    $secondPart = new \ArrayIterator(array('d', 'e', 'f'));
    $combined = new \AppendIterator();
    $combined->append($firstPart);
    $combined->append($secondPart);
 
The idea is simple: An ``\AppendIterator`` is used to concatenate several other iterators. The keys of the source iterators (``$firstPart`` and ``$secondPart``) were not explicitly specified as they are not important. Each source iterator uses numeric keys in the range ``0..2``.

This works as expected in *most* cases:

    foreach ($combined as $value) {
        // This loop iterates over all 6 values.
    }

In contrast to that...

    iterator_to_array($combined);
 
... returns only the 3 values ``["d", "e", "f"]``, as elements with same keys are overwritten.
In a scenario like the one above this behavior is... unexpected at best. 

### Solution ###

The second parameter of ``iterator_to_array()`` controls the handling of keys. When setting it to ``false``, the keys of the traversed object are ignored and a numerically indexed array with all values is returned:

    iterator_to_array($combined, false);

This returns all 6 values (``["a", "b", "c", "d", "e", "f"]``), as the keys are not taken into account.  
However, there is a drawback: Each caller has to be aware of the possible problem and must remember to explicitly define the key handling behavior.

If the desired behavior is a numerically indexed concatenation of several iterators, then that should be modeled explicitly. We can rely on established design patterns and create a simple [decorator](https://en.wikipedia.org/wiki/Decorator_pattern) that enforces the desired key behavior:

    class NumericKeysIterator extends \IteratorIterator {
        /**
         * @var integer
         */
        private $key = 0;
    
        public function rewind() {
            parent::rewind();
            $this->key = 0;
        }
    
        /**
         * @return integer
         */
        public function key() {
            return $this->key;
        }
        
        public function next() {
            parent::next();
            $this->key++;
        }
    }

When plugging this together, we get an iterator whose keys are always ascending integer values:

    $combined = new NumericKeysIterator($combined);
    $items = iterator_to_array($combined);
    
Therefore, ``iterator_to_array()`` returns all 6 items even if the default key handling is used. The caller does not have to think about the edge case with duplicated keys.

### Conclusion ###

Pass ``false`` as second parameter to ``iterator_to_array()``, if you are only interested in the elements of an iterator.  
When designing a ``\Traversable``, think about the desired key behavior and model it explicitly.
