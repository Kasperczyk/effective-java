# Item 4: Enforce noninstantiability with a private constructor

## Examples

### basic example

    public class UtilityClass {

      // suppress default constructor for noninstantiability
      private UtilityClass() {
        throw new AssertionError();
      }
      ...
    }
