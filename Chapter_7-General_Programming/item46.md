# Item 46: Prefer for-each loops over traditional for loops

- the first two examples were the preferred way for iterating pre 1.5
  - better than while loops
  - but iterator and index variables are "just clutter"

## Examples

### Preferred way for iterating over a collection pre 1.5

    for (Iterator i = c.iterator(); i.hasNext()) {
      doSomething((Element) i.next());
    }

### Preferred way for iterating over an array pre 1.5

    for (int i = 0; i < a.length; i++) {
      doSomething(a[i]);
    }

### Preferred way for arrays and collections since 1.5

    for (Element e : elements) {
      doSomething(e);
    }

## The Iterable interface

- the for-each loop lets you iterate over any object that implements the Iterable interface
- if you are writing a type that represents a group of elements, have it implement Iterable (so users can iterate over this type using the for-each loop)


    public interface Iterable<E> {
      // returns an iterator over the elements in this Iterable
      Iterator<E> iterator();
    }


## Three common situations where you can't use a for-each loop

*(?)*

1. Filtering
2. Transforming
3. Parallel iteration
