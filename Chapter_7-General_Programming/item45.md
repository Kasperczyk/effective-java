# Item 45: Minimize the scope of local variables

- the most powerful technique for minimizing the scope of a local variable is to declare it where it is first used
- nearly every local variable declaration should contain an initializer (exception: variable is initialized inside a try block)
- prefer for loops to while loops
- keep methods small and focused

## Examples

### Preferred idiom for iterating over a collection

    for (Element e : c) {
      doSomething(e);
    }

### Another loop idiom that minimizes the scope of local variables

    for (int i = 0, n = expensiveComputation(); i < n; i++) {
      doSomething(i);
    }
