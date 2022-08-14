---
description: Model View Controller Framework
---

# MVC-MVVM

One of software engineering archtecture pattern to isolate domain logic from user interface and permit indepent development, testing and maintenance.

* Model
  * manages the behvaiours and data of the application domain
  * responds to requests for information about its states (usually from the View)
  * responds to instructions to change states (usually from the controller)
  * In event-driven systems, model notifies observers (usually View) when the information changes so that they can react.
* View
  * renders the model into a form suitable for interaction, typically a user interface element
  * Multiple Views can exist for a single model for different purposes.
  * A viewport typically has a one-to-one correspondence with a display surface and knows how to render it.
* Controller
  * receives user input and initiates a response by making calls on model objects
  * A controller accepts input from the user and instructs the model and viewport to perform actions based on that input.

![](https://user-images.githubusercontent.com/73092222/178050022-3a43283a-9748-4584-9a79-29c1888df7e9.jpg)

### Model View View-Model Framework

* Descendant of MVC
* Sometimes called Model-View-Binder
* View model
  * Abstraction of the view that exposes public properties and commands
  * Declarative data binding

![](https://user-images.githubusercontent.com/73092222/178049789-049c6fad-1580-4ab3-8d5f-76eed8e8803e.jpg)

### Reference

[MVC\_MVVM](https://d3c33hcgiwev3.cloudfront.net/\_d563400d0c6122c03c67916ecaa014df\_2-MVC.pdf?Expires=1657065600\&Signature=Jb9t8GXxkIQEK2Y1TfIsRQA4QRGH-oe5Mz7YV57HqGbLOYythAhQVwNenab51A1aRZAgzDBSBV3kb7BVbttdB4BVb1y05EANn4oDltgDEKmpM3EDm9lOPqBxVyB1xnTvfLhKmN9Y2RxCneZjg30NZ7DY6lFXkj\~jXeERcLbOscA\_\&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A)
