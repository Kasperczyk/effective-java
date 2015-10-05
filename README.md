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


# Item 2: Consider a builder when faced with many constructor parameters

## Examples

### basic example

    public class NutritionFacts {

      private final int servingSize;
      private final int servings;
      private final int calories;
      private final int fat;
      private final int sodium;
      private final int carbohydrate;

      // static member class
      public static class Builder {

        // required parameters
        private final int servingSize;
        private final int servings;

        // optional parameters - initialized to default values
        private final int calories = 0;
        private final int fat = 0;
        private final int sodium = 0;
        private final int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
          this.servingSize = servingSize;
          this.servings = servings;
        }

        public Builder calories(int val) {
          calories = val;
          return this;
        }

        public Builder fat(int val) {
          fat = val;
          return this;
        }

        public Builder carbohydrate(int val) {
          carbohydrate = val;
          return this;
        }

        public Builder sodium(int val) {
          sodium = val;
          return this;
        }

        public NutritionFacts build() {
          return new NutritionFacts(this);
        }

      }

      private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
      }

    }

    // client code
    NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
      .calories(100).sodium(35).carbohydrate(27).build();

### (a)

## Explanation

Static factories and constructors do not scale well to large numbers of optional parameters.

## Alternative ways to handle the problem

### Telescoping Constructor pattern

- one constructor with required parameters, and more with optional parameters, first one then two ...
- works, but it is hard to write client code or read it; subtle bugs could appear (wrong order of two parameters of the same type)

### JavaBeans pattern

- parameterless constructor, then using setter methods
- works, but construction is split across multiple calls (inconsistent state partway through its construction) and the pattern precludes the possibility of making a class immutable

## Advantages

- client code is easy to write and read
- a builder can impose invariants on its parameters and check these invariants (if any invariants are violated, the build method should throw an IllegalStateException with an indication on which invariant is violated)

## Disadvantages

## When to use

- when faced with many optional parameters

## Summary



### Info

*(?)* -  further studies necessary!

### mentioned (anti)patterns

- Factory Method pattern
- Flyweight pattern
- Service Provider Framework pattern
- Adapter pattern
- Telescoping Constructor pattern
- Builder pattern

Skeleton:

# Item 2: Consider a builder when faced with many constructor parameters

## Examples

### basic example

### (a)

## Explanation

## Advantages

## Disadvantages

## When to use

## Summary
