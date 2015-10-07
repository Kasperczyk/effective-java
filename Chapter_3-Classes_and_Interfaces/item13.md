# Item 13: Minimize the accessibility of classes and members

- a well-designed module hides all of its implementation details, cleanly separating its API from its implementation
- modules then communicate only through their APIs and are oblivious to each others' inner workings
- concept: information hiding / encapsulation - one of the fundamental tenets of software design
- access control mechanism specifies the accessibility of classes, interfaces, and members
- the accessibility of an entity is determined by the location of its declaration and by which, if any, of the access modifiers (private, protected, and public) is persent on the declaration
- **make each class or member as inaccessible as possible**
- top-level (non-nested) classes and interfaces can have only two access levels: public and package-private
  - by making it package-private, you make it part of the implementation rather than the exported API
  - you can modify, replace, or eliminate it without fear of harming existing clients
  - if it is public, you are obligated to support it forever to maintain compatibility
- if a package-private top-level class or interface is used by only on class, consider making the top-level class a private nested class
- for members (fields, methods, nested classes, and nested interfaces), there are four possible access levels
  - private
  - package-private (default)
  - protected
  - public
- the need for protected members should be relatively rare
- it is acceptable to to make a private member of a public class package-private in order to test it, but not higher than that
- **instance fields should never be public**
- you can expose constants via public static final fields (these fields must contain either primitive values or references to immutable objects)
- it is wrong for a class to have a public static final array field, or an accessor that returns such a field *(1)* *(?)*

## Examples

### (1)

    // potential security hole
    public static final Thing[] VALUES = { ... };

## Advantages

- it decouples the modules that comprise a system
- they can be developed, tested, optimized, used, understood, and modified in isolation
- they can be developed in parallel
- they can be debugged with little fear of harming other modules
- increases software reuse (modules that aren't tightly coupled often prove useful in other contexts besides the ones for which they were developed)
- decreases the risk in building large systems (individual modules may prove successful even if the system does not)


## Summary

You should always reduce accessibility as much as possible. After carefully designing a minimal public API, you should prevent any stray classes, interfaces, or members from becoming part of the API. With the exception of public static final fields, public classes should have no public fields. Ensure that objects referenced by public static final fields are immutable.
