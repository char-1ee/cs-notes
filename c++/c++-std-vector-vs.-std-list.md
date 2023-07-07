# std::vector vs. std::list

[_std::list_](https://en.cppreference.com/w/cpp/container/list) _vs._ [_std::vector_](https://en.cppreference.com/w/cpp/container/vector)

| Vector                                                                             | List                                                                                                                                               |
| ---------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| It has contiguous memory.                                                          | While it has non-contiguous memory.                                                                                                                |
| It is synchronized.                                                                | While it is not synchronized.                                                                                                                      |
| Vector may have a default size.                                                    | List does not have default size.                                                                                                                   |
| In vector, each element only requires the space for itself only.                   | In list, each element requires extra space for the node which holds the element, including pointers to the next and previous elements in the list. |
| Insertion at the end requires constant time but insertion elsewhere is costly.     | Insertion is cheap no matter where in the list it occurs.                                                                                          |
| Vector is thread safe.                                                             | List is not thread safe.                                                                                                                           |
| Deletion at the end of the vector needs constant time but for the rest it is O(n). | Deletion is cheap no matter where in the list it occurs.                                                                                           |
| Random access of elements is possible.                                             | Random access of elements is not possible.                                                                                                         |
| Iterators become invalid if elements are added to or removed from the vector.      | Iterators are valid if elements are added to or removed from the list.                                                                             |
