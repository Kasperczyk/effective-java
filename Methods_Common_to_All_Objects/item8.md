# Item 8: Obey the general contract when overriding equals

- if equals is not overridden, each instance of the class is equal only to itself
  this is right if:
    - each instance of the class is inherently unique
    - you don't care whether the class provides a "logical equality" test
    - a superclass has already overridden equals, and the superclass behavior is appropriate for this class
    - the class is private or package-private, and you are certain that its equals method will never be invoked

in the fourth case should arguably be overridden in case it is accidentally invoked:

    @Override public boolean equals(Object o) {
      throw new AssertionError(); // Method is never called
    }

## Examples

### Violation of Symmetry

    public final class CaseInsensitiveString {
      private final String s;

      public CaseInsensitiveString(String s) {
        if (s == null) {
          throw new NullPointerException();
        }
        this.s = s;
      }

      // Broken - violates symmetry
      @Override public boolean equals(Object o) {
        if (o instanceof CaseInsensitiveString) {
          return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        }
        if (o instanceof String) { // One-way interoperability!
          return s.equalsIgnoreCase((String) o);
        }
        return false;
      }
    }

    CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
    String s = "polish";

    cis.equals(s); // **returns true**
    s.equals(cis); // **returns false**

### Fix

    @Override public boolean equals(Object o) {
      return o instanceof CaseInsensitiveString &&
        ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
    }

### Violation of Transitivity

    public class Point {
      private final int x;
      private final int y;
      public Point(int x, int y) {
        this.x = x;
        this.y = y;
      }
    }

    @Override public boolean equals(Object o) {
      if (!(o instanceof Point)) {
        return false;
      }
      Point p = (Point) o;
      return p.x == x && p.y == y;
    }

    public class ColorPoint extends Point {
      private final Color color;
      public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
      }

      // Broken - violates symmetry
      @Override public boolean equals (Object o) {
        if (!(o instanceof ColorPoint)) {
          return false;
        }
        cp = (ColorPoint) o;
        return super.equals(o) && ((ColorPoint) o).color == color;
      }
    }

    Point p = new Point(1, 2);
    ColorPoint cp = new ColorPoint(1, 2, Color.RED);

    p.equals(cp); // **returns true**
    cp.equals(p); // **returns false**

### Broken Fix

    // Broken - violates Transitivity (method in ColorPoint)
    @Override public boolean equals(object o) {
      if (!(o instanceof Point)) {
        return false;
      }

      // If o is a normal Point, do a color-blind comparison
      if (!(o instanceof ColorPoint)) {
        return o.equals(this);
      }

      // o is a ColorPoint; do a full comparison
      return super.equals(o) && ((ColorPoint) o).color == color;
    }

This approach provides symmetry, but at the expense of Transitivity

    ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
    Point p2 = new Point(1, 2);
    ColorPoint p1 = new ColorPoint(1, 2, Color.BLUE);

    p1.equals(p2); // **returns true**
    p2.equals(p3); // **returns true**
    p1.equals(p3); // **returns false**

## Explanation

## The contract

The equals method implements an equivalent relation. It is (x, y, z are non-null references):

- reflexive: x.equals(x) must return true
- symmetric: x.equals(y) must return true if and only if y.equals(x) returns true
- transitive: if x.equals(y) and y.equals(z), then x.equals(z) must return true
- consistent: multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparison on the object is modified
- x.equals(null) must return false

Many classes, including all collections classes, depend on the object passed to them obeying the equals contract

## Advantages of logical equality in value classes

- satisfies programmer expectations
- enables instances to serve as map keys or set elements with predictable, desirable behavior

## Disadvantages

## When to use

- override equals if a class has a notion of logical equality that differs from mere object identity, and a superclass has not already overridden equals to implement the desired behavior
- generally the case for value classes (a class that represents a value)

## When not to use

- non value classes
- value classes with instance control *(?)*
- enum types

## Summary
