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

There is no way to extend an instantiable class and add a value component while preserving the equals contract, unless you are willing to forgo the benefits of object-oriented abstraction

### Another Broken Fix

    // Broken - violates Liskov substitution principle
    @Override public boolean equals(Object o) {
      if (o == null || o.getClass() ! getClass()) {
        return false;
      }
      Point p = (Point) o;
      return p.x == x && p.y == y;
    }

This has the effect of equating objects only if they have the same implementation class.

    private static final Set<Point> unitCircle;
    static {
      unitCircle = new HashSet<Point>();
      unitCircle.add(new Point(1, 0));
      unitCircle.add(new Point(0, 1));
      unitCircle.add(new Point(-1, 0));
      unitCircle.add(new Point(0, -1));
    }

    public static boolean onUnitCircle(Point p) {
      return unitCircle.contains(p);
    }

Extend Point in a way that doesn't add a value component:

    public class CounterPoint extends Point {
      private static final AtomicInteger counter = new AtomicInteger();

      public CounterPoint(int x, int y) {
        super(x, y);
        counter.incrementAndGet();
      }
      public int numberCreated() {
        return counter.get();
      }
    }

The Liskov substitution principle says that any important property of a type should also hold for its subtypes, so that any method written for the type should work equally well on its subtypes.

Collections use the equals method to check for containment -> no CounterPoint instance is equal to any Point -> problem if we pass a CounterPoint to the onUnitCircle method

### Workaround

    // Adds a value component without violating the equals contract
    public class ColorPoint {
      private final Point point;
      private final Color color;

      public ColorPoint(int x, int y, Color color) {
        if (color == null) {
          throw new NullPointerException();
        }
        point = new Point(x, y);
        this.color = color;
      }

      public Point asPoint() {
        return point;
      }

      @Override public boolean equals(Object o) {
        if (!(o instanceof ColorPoint)) {
          return false;
        }
        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.color.equals(color);
      }
    }

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

## When to use

- override equals if a class has a notion of logical equality that differs from mere object identity, and a superclass has not already overridden equals to implement the desired behavior
- generally the case for value classes (a class that represents a value)

## When not to use

- non value classes
- value classes with instance control *(?)*
- enum types

## Notes

- you can add a value component to a subclass of an abstract class without violating the equals contract
- do not write an equals method that depends on unreliable resources (like a network connection)
- a test for null is unnecessary, instance of returns false if its first operand is null

    @Override public boolean equals(Object o) {
      if (!(o instanceof MyType)) {
        return false;
      }
      MyType mt = (MyType) o;
    }

## Recipe for a high-quality equals method

1. Use the == operator to check if the argument is a reference to this object. If so, return true.
2. Use the instanceof operator to check if the argument has the correct type. If not, return false.
3. Cast the argument to the correct type (guaranteed to succeed because of the instanceof test)
4. For each "significant" field in the class, check if that field of the argument matches the corresponding field of this object. If all these tests succeed, return true, otherwise return false.
  - for primitive fields (not float or double), use the == operator for comparisons
  - for object reference fields, invoke the equals method recursively
  - for float fields, use the Float.compare method
  - for double fields, use the Double.compare method
  - for array fields, apply these guidelines to each element (if every element in an array field is significant, you can use one of the Arrays.equals methods)
  - some object reference fields may legitimately contain null, use the following idiom to prevent NullPointerExceptions
  - for best performance, first compare fields that are more likely to differ, less expensive to compare, or, ideally, both
  - you must not compare fields that are not part of an object's logical state
  - you need not compare redundant fields which can be calculated from significant fields, but can improve performance

    (field == null ? o.field == null : field.equals(o.field))

    // or this alternative that may be faster if field and o.field are often identical
    (field == o.field || (field != null && field.equals(o.field)))

5. when finished writing your equals method, ask yourself three questions
  - is it symmetric?
  - is it transitive?
  - is it consistent?
  - write unit tests

## Final caveats

- always override hashCode when you override equals
- don't substitute another tyoe for Object in the equals declaration -> use the @Override annotation

*(?)* canonical form
