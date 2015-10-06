# Item 5: Avoid creating unnecessary objects

It is often appropriate to reuse a single object instead of creating a new functionally equivalent object each time it is needed. Reuse can be both faster and more stylish. An object can always be reused if it is immutable.

## Examples

### basic example

    // don't do this
    String s = new String("stringette");
    // instead do this
    String s = "stringette";

### complex example

#### not good

    public class Person {
      private final Date birthDate;

      // Other fields, methods, and constructor omitted
      // don't do this
      public boolean isBabyBoomer() {
        // unnecessary allocation of expensive object
        Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomStart = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomEnd = gmtCal.getTime();
        return birthDate.compareTo(boomStart) >= 0 &&
               birthDate.compareTo(boomEnd) < 0;
      }
    }

#### better

    public class Person {
      private final Date birthDate;
      // Other fields, methods, and constructor omitted

      // The starting and ending dates of the baby boom
      private static final Date BOOM_START;
      private static final Date BOOM_END;

      static {
        Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_START = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_END = gmtCal.getTime();
      }

      public boolean isBabyBoomer() {
        return birthDate.compareTo(BOOM_START) >= 0 &&
               birthDate.compareTo(BOOM_END) < 0;
      }       
    }

## Explanation

The improved version creates Calendar, TimeZone, and Date instances only once, instead of creating them every time isBabyBoomer is invoked.

## Advantages

- significant performance gains
- improved clarity (static final fields == constants)

## Disadvantages

- if isBabyBoomer is never invoked, the fields will be initialized unnecessarily (not that bad)

## Other notes

- prefer primitives to boxed primitives, and watch out for unintentional autoboxing
