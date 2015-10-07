# Item 47: Know and use the libraries

- use libraries in preference to ad hoc implementations!
- every programmer should be familiar with the contents of java.lang, java.util, and java.io
- knowledge of other libraries can be aquired on an as-needed basis
- important:
  - Collections Framework
  - java.util.concurrent (high-level part)

## Advantages

- by using a standard library, you take advantage of the knowledge of the experts who wrote it and the experience of those who used it before you
- you can spend your time working on your application rather than on the underlying plumbing
- performance tends to improve over time, with no effort on your part
- libraries tend to gain new functionality over time
- code is more readable, maintainable, and reusable by a multitude of developers

## Summary

Don't reinvent the weel. If you need to do something that seems like it should be reasonably common, there may already be a class in the libraries that does what you want. If there is, use it. If you don't know, check.
