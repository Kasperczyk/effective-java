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
- they can have multiple varargs parameters (up to one per setter method)
- they can fill in some fields automatically (such as a serial number that increases each time an object is created)
- they make a fine abstract factory *(?)*

## Disadvantages

- in order to create an object, you must first create its builder (performance)
- more verbose than the telescoping constructor pattern

## When to use

- when faced with many optional parameters (four or more)
- often good to start with a builder because parameter lists may get larger

## Summary

The builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters, especially if most of those parameters are optional. Client code is much easier to read and write with builders than with the traditional telescoping constructor pattern, and builders are much safer than JavaBeans.
