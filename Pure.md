```java
public static double sigma(double... ns) {
  var mu = mean(ns);
  var deviations =
    Arrays.stream(ns).map(x->(x-mu)*(x-mu)).boxed().mapToDouble(x->x);
  double variance = deviations.sum() / ns.length;
  return Math.sqrt(variance);
}
```

```java
public static double sigma(double... ns) {
  double mu;
  mu = mean(ns);
  DoubleStream deviations;
  deviations =
    Arrays.stream(ns).map(x->(x-mu)*(x-mu)).boxed().mapToDouble(x->x);
  double variance;
  variance = deviations.sum() / ns.length;
  return Math.sqrt(variance);
}
```

```java
public static double sigma(double... ns) {
  double mu = mean(ns);
  double variance = 0;
  for (double n : ns) {
    var deviation = n - mu;
    variance += deviation * deviation;
  }
  variance /= ns.length;
  return Math.sqrt(variance);
}
```

```java
public static double sigma(double... ns) {
  return new SigmaCalculator(ns).invoke();
}

private static class SigmaCalculator {
  private double[] ns;
  private double mu;
  private double variance = 0;
  private double deviation;

  public SigmaCalculator(double... ns) {
    this.ns = ns;
  }

  public double invoke() {
    mu = mean(ns);
    for (double n : ns) {
      deviation = n - mu;
      variance += deviation * deviation;
    }
    variance /= ns.length;
    return Math.sqrt(variance);
  }
}
```
