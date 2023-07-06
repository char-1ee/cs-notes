# hashCode() and equals()

#### Same usages

Native methods in `Object` class to compare two objects.

#### Whats the difference?&#x20;

* _equals()_ compares by fields in class&#x20;
* _hashCode()_ compares by hashCode generated, faster than former

#### So why we need equals()?

* If _equals()_ then must be _hashCode()_ equals
* If _hashCode()_ equals cannot ensure real _equals()_ because of hash collsions

#### How to  do large and speedy comparisons efficiently?

1. first compare by _hashCode()_&#x20;
2. if _hashCode()_ differs, two objects absolutely different
3. else compare by _equals()_

Such comparison strategy is applied in HashSet, HashMap etc.

#### Override

* Since _hashCode()_ in Object class returns object address, so need override.
* Good practices are once override _hashCode()_, override _equals(_) too.
* Only need override _hashCode(_) and _equals(_) when class objecta will be placed into HashSet, HashMap, HashTable etc. hash structures.
