```java
/**
 * Returns an array of month names.
 *
 * @param shortened a flag indicating that shortened month names should
 *                  be returned.
 * @return an array of month names.
 */
public static String[] getMonths(final boolean shortened) {

  if (shortened) {
    return DATE_FORMAT_SYMBOLS.getShortMonths();
  } else {
    return DATE_FORMAT_SYMBOLS.getMonths();
  }
}
```
