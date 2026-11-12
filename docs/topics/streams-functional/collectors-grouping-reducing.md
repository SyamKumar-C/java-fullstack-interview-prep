# Java Collectors: collect, groupingBy, partitioningBy and reduce

## 1. What is a Collector?

A Collector describes how stream elements are accumulated into a result. The common terminal operation is `collect()`. Collectors can create Lists, Sets, Maps, counts, summaries, grouped results, or custom containers.

```java
List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toList());
```

Common collectors: `toList`, `toSet`, `toMap`, `toCollection`, `counting`, `joining`, `mapping`, `filtering`, `flatMapping`, `groupingBy`, `partitioningBy`, `summarizingInt`, `summingInt`, `averagingInt`, `maxBy`, `minBy`, `reducing`, and `collectingAndThen`.

## 2. toList, toSet and joining

```java
List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toList());

Set<String> departments = employees.stream()
    .map(Employee::getDepartment)
    .collect(Collectors.toSet());

String csv = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.joining(", "));
```

Use `toCollection(ArrayList::new)` when you specifically need a mutable ArrayList. Do not rely on an unspecified concrete type from `Collectors.toList()`.

## 3. groupingBy: classify into multiple groups

`groupingBy` applies a classifier function and collects elements with the same key.

```java
Map<String, List<Employee>> byDepartment =
    employees.stream().collect(
        Collectors.groupingBy(Employee::getDepartment));
```

### Count each group

```java
Map<String, Long> countByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.counting()));
```

### Sum and average

```java
Map<String, Integer> salaryTotalByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summingInt(Employee::getSalary)));

Map<String, Double> averageSalaryByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.averagingInt(Employee::getSalary)));
```

### Transform values inside each group with mapping

```java
Map<String, Set<String>> namesByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(Employee::getName, Collectors.toSet())));
```

### Get summary statistics per group

```java
Map<String, IntSummaryStatistics> statsByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summarizingInt(Employee::getSalary)));
```

The statistics provide count, sum, min, max and average.

## 4. Nested grouping

Use a downstream grouping collector when more than one classification level is needed.

```java
Map<String, Map<String, List<Employee>>> byDepartmentAndCity =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.groupingBy(Employee::getCity)));
```

This creates department -> city -> employees.

## 5. partitioningBy: exactly two groups

`partitioningBy` uses a boolean predicate and creates true and false groups.

```java
Map<Boolean, List<Employee>> activeGroups = employees.stream()
    .collect(Collectors.partitioningBy(Employee::isActive));
```

With a downstream collector:

```java
Map<Boolean, Long> activeCounts = employees.stream()
    .collect(Collectors.partitioningBy(
        Employee::isActive, Collectors.counting()));
```

Use partitioning for pass/fail, active/inactive, or above/below threshold. Use groupingBy for departments, cities, status names, or any number of categories.

## 6. groupingBy vs partitioningBy

| groupingBy | partitioningBy |
|---|---|
| Classifier returns a key | Predicate returns boolean |
| Many possible groups | True and false groups |
| Example: department | Example: salary >= 100000 |

## 7. toMap and duplicate keys

`toMap` needs a unique key unless you supply a merge function.

```java
Map<Long, Employee> byId = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,
        Function.identity()));
```

If two employees have the same ID, this throws `IllegalStateException`. Handle duplicates deliberately:

```java
// Keep the employee with the higher salary for each department
Map<String, Employee> highestPaidByDepartment = employees.stream()
    .collect(Collectors.toMap(
        Employee::getDepartment,
        Function.identity(),
        (left, right) -> left.getSalary() >= right.getSalary()
            ? left : right));
```

The third argument is the merge function. A fourth argument can supply a map factory, for example `TreeMap::new`, when sorted keys are required.

## 8. reducing vs reduce

Stream's `reduce` is a terminal operation. `Collectors.reducing` is a collector, often useful downstream of grouping. Prefer specialized collectors such as `counting`, `summingInt`, and `maxBy` when they express the goal more clearly.

```java
int totalSalary = employees.stream()
    .mapToInt(Employee::getSalary)
    .sum();

int totalUsingReduce = employees.stream()
    .map(Employee::getSalary)
    .reduce(0, Integer::sum);
```

For safe parallel reduction, the combining operation must be associative, and the identity must be a true identity. Associative means changing grouping does not change the result. Integer addition is mathematically associative, but overflow still applies.

## 9. collectingAndThen and unmodifiable results

`collectingAndThen` runs a finishing transformation after the downstream collector completes.

```java
Set<String> immutableNames = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.collectingAndThen(
        Collectors.toSet(), Set::copyOf));
```

This makes an unmodifiable copy. Remember that unmodifiable containers do not automatically make mutable elements deeply immutable.

## 10. Real interview scenario: department summary

Requirement: return each department's employee count and average salary. A summary record keeps the result clear:

```java
record DepartmentSummary(long count, double averageSalary) {}

Map<String, DepartmentSummary> summary = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.collectingAndThen(
            Collectors.summarizingInt(Employee::getSalary),
            stats -> new DepartmentSummary(
                stats.getCount(), stats.getAverage()))));
```

This groups in one stream pass and calculates summary values for each department.

## 11. Common mistakes

1. Forgetting a merge function when duplicate keys are possible in toMap.
2. Using partitioningBy for more than two categories.
3. Assuming groupingBy returns a sorted map.
4. Using reducing when summingInt or counting is simpler.
5. Using a non-associative operation in a parallel reduction.
6. Forgetting that a downstream collector works separately inside each group.
7. Treating unmodifiable results as deeply immutable.

## 12. Interview questions

**Q1. What is a downstream collector?** A collector applied to each group produced by groupingBy or partitioningBy.

**Q2. How do you count employees per department?** groupingBy(Employee::getDepartment, counting()).

**Q3. How do you handle duplicate keys in toMap?** Supply a merge function that decides which value to keep or how to combine them.

**Q4. groupingBy vs partitioningBy?** groupingBy can create many keys; partitioningBy creates true and false groups based on a predicate.

**Q5. Why must a parallel reduction be associative?** The stream may split input and combine partial results in a different grouping. Associativity makes the result independent of grouping.

**Q6. How do you get count, sum, min, max and average together?** Use summarizingInt, summarizingLong or summarizingDouble.

## 13. Practice problems

1. Count employees by department.
2. Calculate average salary by department.
3. Get unique employee names by department.
4. Partition numbers into even and odd.
5. Convert orders into customerId -> totalAmount, handling repeated customers.
6. Find the highest-paid employee in each department.
7. Group employees by department and then city.
8. Return department count, min salary, max salary and average salary.
