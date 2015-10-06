# Item 1: Consider static factory methods instead of constructors

## Examples

### basic example

    public static Boolean valueOf(boolean b) {
        return b ? Boolean.TRUE : Boolean.FALSE;
    }

### (a)

    public static <K, V> HashMap<K, V> newInstance() {
        return new HashMap<K, V>();
    }

    Map<String, List<String>> m = HashMap.newInstance();

instead of

    Map<String, List<String>> m = new HashMap<String, List<String>>();

## Explanation

- instead of, or in addition to, constructors

## Advantages

- they have names (more expressive)
- multiple static factory methods with the same signature possible (impossible when using constructors)
- aren't required to create a new object when invoked (instance controlled classes; singleton, noninstantiable, immutable)
- they can return an object of any subtype of their return type
- they reduce the verbosity of creating parameterized type instances (type inference) *(a)*

## Disadvantages

- classes without public or protected constructors cannot be subclassed
- they are not distinguishable from other static methods (they do not stand out like constructors) -> use common names for static factory methods

### Common names for static factory methods

- valueOf (type-conversion methods)
- of (concise alternative to valueOf)
- getInstance (in case of a singleton it takes no parameters and returns the sole instance)
- newInstance (like getInstance, but each instance returned is distinct from all others)
- get**Type** (like getInstance, but used when the factory method is in a different class. Type indicates the type of object returned)
- new**Type** (like newInstance, but used when the factory method is in a differen class. Type indicates the type of object returned)

## When to use

- if a class requires multiple constructors with the same signature, replace them with static factory methods
- if you want to be able to return the same object from repeated invocations
- service provider framework *(?)*
- for parameterized classes *(a)*

## Summary

Static factory methods and public constructors both have their uses, and it pays to understand their relative merits. Often static factories are preferable, so avoid the reflex to provide public constructors without first considering static factories.
