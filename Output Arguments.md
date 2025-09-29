```c++
void stats(double* ns, int n, double& mean, double& sum) {
  sum=0;
  for (int i=0; i<n; i++)
    sum += ns[i];
  mean=sum/n;
}
```

```java
stats.calculate(list);
double mean = stats.getMean();
double sum = stats.getSum()
```
