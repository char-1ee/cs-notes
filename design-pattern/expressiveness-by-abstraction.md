---
description: Write expressive code by raising the levels of abstraction
---

# Expressiveness by Abstraction

1. Understand the code and categorize whole program into components
2. Put a label over each components (each of things the code does)
3. Replace the corresponding code with it

### Example

```cpp
int computeNumberOfBreaks(const std::vector<City>& route)
{
    static const double MaxDistance = 100;

    int nbBreaks = 0;
    for (vector<City>::const_iterator it1 = route.begin(), it2 = route.end();
         it1 != route.end();
         it2 = it1, ++it1)
    {
        if (it2 != route.end())
        {
            if(it1->getGeographicalAttributes().getLocation().distanceTo(
            it2->getGeographicalAttributes().getLocation()) > MaxDistance)
            {
                ++nbBreaks;
            }
        }
    }
    return nbBreaks;
}
```

Noticed that the shown iteration is a trick to manipulate adjacent elements in a collection. it1 starts at the beginning, and it2 point to the element right before it1 all along the traversal.

This code can be labeled as&#x20;

* Manipulate consecutive elements together,
* Determine if cities are farther away than MaxDistance,
* Count the number of times a condition is satisfied.

Then accordingly, we can&#x20;

* For manipulating consecutive elements, we can create a component that we would call “**consecutive**”, and that would transform a collection of elements into a collection of element _pairs_, each pair having an element of the initial collection and the one next to it. For instance, if route contains {A, B, C, D, E}, `consecutive(routes)` would contain {(A,B), (B,C), (C, D), (D, E)}.
* For determining whether two consecutive cities are farther away from each other than MaxDistance, we can simply use a function object (functor) or lambda that we would call **FartherThan.**
* For counting the number of times a condition is satisfied, we can just use the STL algorithm **count\_if**.

Then the refactored code:

```cpp
int computeNumberOfBreaks(const std::vector<City>& route)
{
    static const double MaxDistance = 100;

    return count_if(consecutive(route), FartherThan(MaxDistance));
}
```

### Reference

{% embed url="https://www.fluentcpp.com/2017/01/03/super-expressive-code-by-raising-levels-of-abstraction/" %}
