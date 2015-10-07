# Item 49: Prefer primitive types to boxed primitives

- Java has a two-part type system
  - primitives such as int, double, and boolean
  - reference types such as List, String
  - every primitive has a corresponding reference type, called a boxed primitive

## Differences

1. primitives have only their values, whereas boxed primitives have identities distinct from their values (can have the same value and different identities)
2. primitive types have only fully functional values, whereas each boxed primitive type has one nonfunctional value (null)
3. primitives are generally more time- and space-efficient than boxed primitives

### Broken comparator

    Comparator<Integer> naturalOrder = new Comparator<Integer>() {
      public int compare(Integer first, Integer second) {
        // == performs an identity comparison on the two object references
        return first < second ? -1 : (first == second ? 0 : 1);
      }
    };

    // should be 0, but returns 1
    naturalOrder.compare(new Integer(42), new Integer(42));

**Applying the == operator to boxed primitives is almost always wrong!**

### Fix

    Comparator<Integer> naturalOrder = new Comparator<Integer>() {
      public int compare(Integer first, Integer second) {
        int f = first, s = second; // Auto-unboxing
        return f < s ? -1 : (f == s ? 0 : 1); // No unboxing
      }
    };

### NullPointerException

    public class Unbelievable {
      static Integer i;

      public static void main(String[] args) {
        if (i == 42) { // throws NullPointerException
          System.out.println("Unbelievable");
        }
      }
    }

- object reference fields' initial value is null
- when you mix primitives and boxed primitives in a single operation, the boxed primitive is auto-unboxed
- if a null object reference is auto-unboxed, you get a NullPointerException
- fix by declaring the variable to be int

### Slow program example

    public static void main(String[] args) {
      Long sum = 0L;
      for (long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
      }
      System.out.println(sum);
    }

- repeated unboxing and boxing -> performance issue

## When to use boxed primitives

- as elements, keys, and values in collections (you can't put primitives in collections -> you must use boxed primitives as type parameters in parameterized types)
- when making reflective method invocations
