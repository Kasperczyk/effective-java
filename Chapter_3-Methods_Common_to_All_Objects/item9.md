# Item 9: Always override hashCode when you override equals

- you must override hashCode in every class that overrides equals! Failure to do so will prevent your class from functioning properly in conjunction with all hash-based collections

## Example

    public final class PhoneNumber {
      private final short areaCode;
      private final short prefix;
      private final short lineNumber;

      public PhoneNumber(int areaCode, int prefix, int lineNumber) {
        rangeCheck(areaCode, 999, "area code");
        rangeCheck(prefix, 999, "prefix");
        rangeCheck(lineNumber, 9999, "line number");
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNumber = lineNumber;
      }

      private static void rangeCheck(int arg, int max, String name) {
        if (arg < 0 || arg > max) {
          throw new IllegalArgumentException(name + ": " + arg);
        }
      }

      @Override public boolean equals(Object o) {
        if (o == this) {
          return true;
        }
        if (!(o instanceof PhoneNumber)) {
          return false;
        }
        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNumber == lineNumber
          && pn.prefix == prefix
          && pn.areaCode == areaCode;
      }

      // Broken - has no hashCode method!
      ...
    }

    Map<PhoneNumber, String> m = new HashMap<PhoneNumber, String>();
    m.put(new PhoneNumber(707, 867, 5309), "Jenny");
    m.get(new PhoneNumber(707, 867, 5309), "Jenny"); // **returns null**

## Recipe

1. Store some constant nonzero value in an int variable called result
2. for each significant field f in the object do the following
  1. compute an int hash code c for the field:
    - boolean: (f ? 1 : 0)
    - byte, char, short, int: (int) f
    - long: (int) (f ^ (f >>> 32))
    - float: Float.floatToIntBits(f)
    - double: Double.doubleToLongBits(f) and (int) (f ^ (f >>> 32)) the result
    - object reference: if the value is null return 0 else recursively invoke hashCode
    - array: do the above recursively for each significant element
  2. combine the hash code c computed before into result as follows:
    - result = 31 * result + c;
3. return result
4. when finished, ask yourself whether equal instances have equal hash codes (write unit tests!)


    @Override public int hashCode() {
      int result = 17;
      result = 31 * result + areaCode;
      result = 31 * result + prefix;
      result = 31 * result + lineNumber;
      return result;
    }

## When to use

- override hashCode when you override equals!

## Notes

- if a class is immutable and the cost of computing the hash code is significant, you might consider caching the hash code in the object
- if you believe that most objects of this type will be used as hash key, then you should calculate the hash code when the instance is created
- otherwise you might choose to lazily initialize it the first time hashCode is invoked *(?)*


    private volatile int hashCode;

    @Override public int hashCode() {
      int result = hashCode;
      if (result == 0) {
        result = 17;
        result = 31 * result + areaCode;
        result = 31 * result + prefix;
        result = 31 * result + lineNumber;
      }
      return result;
    }
