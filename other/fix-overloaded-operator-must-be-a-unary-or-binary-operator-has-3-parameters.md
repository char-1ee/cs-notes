# fix: overloaded 'operator\*' must be a unary or binary operator (has 3 parameters)

> It sounds like you have declared your operator as a member function. A member function takes an implicit first parameter, meaning your operator now takes three parameters. You can fix this by making it a non-member function.
>
> In any case, _it is preferable to declare it as a non-member_, to ensure symmetry between the LHS and the RHS of the operation.
>
> As for [`std::move`](http://en.cppreference.com/w/cpp/utility/move), it is in the `<utility>` header. Although I can't see the reason to use it here.

### Reference&#x20;

[overloaded 'operator+' must be a unary or binary operator error](https://stackoverflow.com/questions/13554320/overloaded-operator-must-be-a-unary-or-binary-operator-error)
