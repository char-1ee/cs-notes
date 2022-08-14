---
description: Or Spiral Rule.
---

# Clockwise Rule

* `int*` - pointer to int
* `int const *` - pointer to const int
* `int * const` - const pointer to int
* `int const * const` - const pointer to const int

Now the first const can be on either side of the type so:

* `const int * == int const *`
* `const int * const == int const * const`

If you want to go really crazy you can do things like this:

* `int **` - pointer to pointer to int
* `int ** const` - a const pointer to a pointer to an int
* `int * const *` - a pointer to a const pointer to an int
* `int const **` - a pointer to a pointer to a const int
* `int * const * const` - a const pointer to a const pointer to an int

### References

* [What is the difference between const int\*, const int \* const, and int const \*?](https://stackoverflow.com/questions/1143262/what-is-the-difference-between-const-int-const-int-const-and-int-const)
* [The Clockwise/Spiral rule by David Anderson](https://c-faq.com/decl/spiral.anderson.html)
