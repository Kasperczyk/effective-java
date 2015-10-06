# Item 6: Eliminate obsolete object references

## Examples

### memory leak example

    public class Stack {
      private Object[] elements;
      private in size = 0;
      private static final int DEFAULT_INITIAL_CAPACITY = 16;

      public Stack() {
        elements = new Object(DEFAULT_INITIAL_CAPACITY);
      }

      public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
      }

      public Object pop() {
        if (size == 0) {
          throw new EmptyStackException();
        }
        return elements[--size];
      }

      private void ensureCapacity() {
        if (elements.length == size) {
          elements = Array.copyOf(elements, 2 * size + 1);
        }
      }
    }

### fix

    public Object pop() {
      if (size == 0) {
        throw new EmptyStackException();
      }
      Object result = elements[--size];
      elements[size] = null; // eliminate obsolete reference
      return result;
    }    

## Explanation

- unintentional object retentions (no garbage collection)

## Advantages

- no memory leak ...
- nulling out obsolete references results in a NullPointerException if they are dereferenced by mistake

## When to use

- whenever a class manages its own memory, the programmer should be alert for memory leaks
- other common source of memory leaks is caches, listeners and other callbacks
