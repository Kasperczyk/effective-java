# Item 3: Enforce the singleton property with a private constructor or an enum type

## Examples

Before release 1.5 there were two ways to implement singletons, both based on keeping the constructor private and exporting a static member to provide access to the sole instance. As of release 1.5 you can simply make an enum type with one element.

### Singleton with public final field

    public class Elvis {
      public static final Elvis INSTANCE = new Elvis();
      private Elvis() { ... }
      public void leaveTheBuilding() { ... }
    }

### Singleton with static factory

    public class Elvis {
        private static final Elvis INSTANCE = new Elvis();
        private Elvis() { ... }
        public static Elvis getInstance() {
          return INSTANCE;
        }
        public void leaveTheBuilding() { ... }
    }

### Enum singleton - the preferred approach

    public enum Elvis {
      INSTANCE;

      public void leaveTheBuilding() { ... }
    }

## Explanation

A singleton is a class that is instantiated exactly once

## Advantages

- the main advantage of the public field approach is that the declarations make it clear that the class is a singleton
- the factory method approach gives you the flexibility to change your mind about whether the class should be a singleton without changing its API

## When to use

If you want only one unique instance of a class

## Summary

The enum approach is the preferred one - it is ...
- functionally equivalent to the public field approach
- more concise
- provides the serialization machinery for free
- provides an ironclad guarantee against multiple instantiation
- protects against serialization or reflection attacks
