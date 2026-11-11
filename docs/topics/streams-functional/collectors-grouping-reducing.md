# Collectors: groupingBy, partitioningBy and Reductions

## groupingBy
Groups elements by a classifier.
```java
Map<String,Long> counts = employees.stream().collect(
  Collectors.groupingBy(Employee::department, Collectors.counting()));
```
Downstream collectors include counting, summingInt, mapping, averaging and toSet.

## partitioningBy
Creates exactly two Boolean groups based on a Predicate.
```java
Map<Boolean,List<Integer>> parts = numbers.stream()
  .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

## reduce
Use reduce to combine values into one result with an associative operation and correct identity. Use collect for mutable containers such as lists/maps; do not mutate a shared object inside reduce.

```java
int total = numbers.stream().reduce(0, Integer::sum);
```

**Interview:** groupingBy vs partitioningBy? Arbitrary classifier keys vs two Boolean groups. Why associative? Parallel grouping must give the same result.
