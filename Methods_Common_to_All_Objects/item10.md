# Item 10: Always override toString

- Object's implementation returns the class name followed by an @ and the unsigned hexadecimal representation of the hash code, e.g. "PhoneNumber@163b91"
- the returned string should be "a concise but informative representation that is easy for a person to read"
- "it is recommended that all subclasses override this method"
- while not as important as obeying the equals and hashCode contracts, providing a good toString implementation makes your class much more pleasant to use
- toString is automatically invoked when an object is passed to println, printf, the string concatenation operator, or assert, or printed by a debugger
- when practical, the toString method should return all of the interesting information contained in the object
- the string should be self-explanatory / should return a summary
- format *(?)* / todo
