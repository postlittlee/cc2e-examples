```java
public static double addAtoBandDivideBy2(double a, double b) {
  return (a+b)/2.0;
}
```

```java
public static double average(double a, double b) {
  return (a+b)/2.0;
}
```

```java
public static double average(double... ns ) {
  return Arrays.stream(ns).sum()/ns.length;
}
```

```java
public void addSalesReceipt(SalesReceipt salesReceipt) {
  salesReceipts.add(salesReceipt);
}
```

```java
public class CommissionedEmployee extends Employee {
  private List<SalesReceipt> salesReceipts = new ArrayList<>();
  //…
  public void addSalesReceipt(SalesReceipt salesReceipt) {
    salesReceipts.add(salesReceipt);
  }
}
```
