---
title: "Lost In Iteration"
---

**TL;DR**: tbd

When writing tests I often find myself using ``iterator_to_array`` to quickly inspect the items in a traversable object:

    $items = iterator_to_array($myTraversableObject);

Converting an object into an array this way allows me to rely on the huge amount of PHP's array functions for further checks.

However, there is a catch when using ``iterator_to_array`` without its second argument: Usually your are only interested in the elements, but ``iterator_to_array`` keeps the keys of the ``\Traversable`` per default. And it is perfectly fine when a ``\Traversable`` uses the same key multiple times.  
Wonder how this can happen (accidentally)? Have a look at this simple example:
 
    $firstPart = new \ArrayIterator(array('a', 'b', 'c'));
    $secondPart = new \ArrayIterator(array('d', 'e', 'f'));
    $combined = new \AppendIterator();
    $combined->append($firstPart);
    $combined->append($secondPart);
 
The idea is simple: An ``\AppendIterator`` is used to concatenate several other iterators. The keys of the source iterators (``$firstPart`` and ``$secondPart``) were not explicitly specified as they are not important. Each source iterator uses numeric keys in the range ``0..2``.



    foreach ($combined as $value) {
        // This loop iterates over all 6 values.
    }
 
      // This returns only 3 values as items with same keys are overwritten.
      // This behavior is... unexpected at best.
      $items = iterator_to_array($combined);
 
      // This returns all 6 values as the keys are not taken into account:
      $items = iterator_to_array($combined, false);
 
      // This returns all 6 items and the caller of the iterator does not have
      // to think about duplicate-key edge cases:
      $items = iterator_to_array(new NumericKeysIterator($combined));

iterator_to_array( ..., true) if only interested in elements
think about the keys of your traversables


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
         * @return int
         */
        public function key() {
            return $this->key;
        }
        
        public function next() {
            parent::next();
            $this->key++;
        }
    }
