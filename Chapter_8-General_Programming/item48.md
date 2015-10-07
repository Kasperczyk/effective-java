# Item 48: Avoid float and double if exact answers are required

- float and double do not provide exact results and should not be used where exact results are required
- the float and double types are particularly ill-suited for monetary calculations
- use BigDecimal, int, or long for monetary calculations

## Examples

### Broken example - floating point for monetary calculation

    public static void main(String[] args) {
      double funds = 1.00;
      int itemsBought = 0;
      for (double price = .10; funds >= price; price += .10) {
        funds -= price;
        itemsBought++;
      }
      // prints out "3"
      System.out.println(itemsBought + " items bought.");
      // prints out "$0.3999999999999999"
      System.out.println("Change: $" + funds);
    }

### Fixed example using BigDecimal

    public static void main(String[] args) {
      final BigDecimal TEN_CENTS = new BigDecimal(".10");
      int itemsBought = 0;
      BigDecimal funds = new BigDecimal("1.00");
      for (BigDecimal price = TEN_CENTS; funds.compareTo(price) >= 0;
            price = price.add(TEN_CENTS)) {
        itemsBought++;
        funds = funds.substract(price);
      }
      // prints out "4"
      System.out.println(itemsBought + " items bought.");
      // prints out "$0.00"
      System.out.println("Money left over: $" + funds);
    }

- less convenient and slower

### Fixed example using int

    private static void main(String[] args) {
      int itemsBought = 0;
      int funds = 100;
      for (int price = 10; funds >= price; price += 10) {
        itemsBought++;
        funds -= price;
      }
      // prints out "4"
      System.out.println(itemsBought + " items bought.");
      // prints out "0 cents"
      System.out.println("Money left over: " + funds + " cents");
    }

## When to use

- use BigDecimal if you want the system to keep track of the decimal point and you don't mind the inconvenience and cost of not using a primitive type
- has the added advantage that it gives you full control over rounding
- if performance is of the essence, you don't mind keeping track of the decimal point yourself, and the quantities aren't too big, use int or long
- int if the quantities don't exceed nine decimal digits
- long if the quantities don't exceed eighteen decimal digits
- more than eighteen decimal digits forces you to use BigDecimal
