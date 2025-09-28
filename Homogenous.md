```java
public static int orientation(Point a, Point b, Point c) {
  double val = (b.x() - a.x()) * (c.y() - a.y()) -
                (b.y() - a.y()) * (c.x() - a.x());
  if (val == 0) {
    return 0;
  }
  return (val > 0) ? +1 : -1;
}
```

```java
public static int orientation(Point a, Point b, Point c) {
  double val = (b.x() - a.x()) * (c.y() - a.y()) -
               (b.y() - a.y()) * (c.x() - a.x());
  return sign(val);
}

private static int sign(double n) {
  if (n == 0) {
    return 0;
  }
  return (n > 0) ? +1 : -1;
}
```

```java
public enum Chirality {left, right, none};

public static Chirality getChirality(Point a, Point b, Point c) {
  return chirality(crossProduct(a, b, c));
}

private static double crossProduct(Point a, Point b, Point c) {
  double abx = b.x() - a.x();
  double aby = b.y() - a.y();
  double acx = c.x() - a.x();
  double acy = c.y() - a.y();
  return abx * acy - aby * acx;
}

private static Chirality chirality(double n) {
  if (n == 0) {
    return none;
  }
  return (n > 0) ? left : right;
}
```
