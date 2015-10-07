# Item 50: Avoid strings where other types are more appropriate

- strings are designed to represent text
- strings are poor substitutes for other value types
- if there is an appropriate value type, whether primitive or object reference, you should use it. If there isn't, you should write one.
- strings are poor substitutes for enum types
- strings are poor substitutes for aggregate types *(1)*
- strings are poor substitutes for capabilities *(?)*

## Examples

### (1)

    // inappropriate use of string as aggregate type
    String compoundKey = className + "#" + i.next();

A better approach is simply to write a class to represent the aggregate, often a private static member class.
