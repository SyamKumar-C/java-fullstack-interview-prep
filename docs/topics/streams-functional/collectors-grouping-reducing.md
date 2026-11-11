# Java Collectors: groupingBy, partitioningBy, mapping and reducing

## 1. What is a Collector?

A Collector describes how a Stream pipeline accumulates elements into a result. The common terminal operation is `collect()`.

```java
List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toList());
```

A collector can build a List, Set, Map, summary statistics, grouped results, or a custom container.

## 2. groupingBy

`groupingBy` classifies elements by a key and collects each group.

```java
Map<String, List<Employee>> byDepartment =
    employees.stream().collect(Collectors.groupingBy(Employee::getDepartment));
```

Result type: department -> employees in that department.

### Count each group

```java
Map<String, Long> countByDepartment =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment, Collectors.counting()));
```

### Sum or average within each group

```java
Map<String, Integer> salaryByDepartment =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summingInt(Employee::getSalary)));

Map<String, Double> averageSalary =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.averagingInt(Employee::getSalary)));
```

### mapping downstream collector

Use `mapping` when the group should contain a transformed value rather than the original object.

```java
Map<String, Set<String>> namesByDepartment =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(Employee::getName, Collectors.toSet())));
```

### maxBy / minBy

```java
Map<String, Optional<Employee>> highestPaidByDepartment =
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.maxBy(Comparator.comparingInt(Employee::getSalary))));
```

The Optional exists because a group collector's result type must represent the possibility of no maximum value in general collector composition. You can also use `collectingAndThen` when you need to transform a downstream result.

## 3. partitioningBy

`partitioningBy` splits elements into exactly two groups based on a predicate: true and false.

```java
Map<Boolean, List<Employee>> salaryGroups =
    employees.stream().collect(Collectors.partitioningBy(
        e -> e.getSalary() >= 100_000));
```

Use it for binary conditions such as active/inactive or pass/fail. Use groupingBy for multiple category values.

## 4. groupingBy versus partitioningBy

| groupingBy | partitioningBy |
|---|---|
| Uses a classifier function | Uses a boolean predicate |
| Can produce many keys | Exactly true and false groups |
| Example: department | Example: salary above threshold |

## 5. reducing

`reducing` combines values using an associative operation. It is useful as a downstream collector, although specialized collectors such as `counting`, `summingInt`, and `maxBy` are usually clearer.

```java
int totalSalary = employees.stream()
    .collect(Collectors.reducing(0, Employee::getSalary, Integer::sum));
```

The operation should be associative for safe parallel reduction: grouping the operations differently must give the same result. Integer addition is associative mathematically, but integer overflow still applies.

## 6. toMap and duplicate keys

```java
Map<Long, Employee> byId = employees.stream()
    .collect(Collectors.toMap(Employee::getId, Function.identity()));
```

This throws IllegalStateException if duplicate keys occur. Supply a merge function when duplicates are valid:

```java
Map<String, Integer> totalByDepartment = employees.stream()
    .collect(Collectors.toMap(
        Employee::getDepartment,
        Employee::getSalary,
        Integer::sum));
```

## 7. collectingAndThen

It applies a finishing transformation after collecting.

```java
Set<String> immutableNames = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.collectingAndThen(
        Collectors.toSet(), Set::copyOf));
```

## 8. Real interview scenario

Given employees, return a map containing each department's employee count and average salary. Use groupingBy with a downstream collector or create a small summary record and map each group to it. Explain the trade-off: a single grouping pass is efficient and expresses the business grouping directly.

## 9. Common mistakes

- Using partitioningBy when there are more than two categories.
- Forgetting to provide a merge function to toMap when duplicate keys are possible.
- Using reducing where summingInt is clearer.
- Assuming groupingBy returns a sorted map; its default map type does not promise sorted order.
- Using a non-associative operation in parallel reductions.

## 10. Interview questions and answers

**Q1. Difference between groupingBy and partitioningBy?**  
groupingBy groups by a classifier and may create many keys. partitioningBy uses a predicate and creates true/false groups.

**Q2. How do you count employees per department?**  
Use groupingBy(Employee::getDepartment, counting()).

**Q3. How do you handle duplicate keys in toMap?**  
Pass a merge function such as Integer::sum or choose the record to keep.

**Q4. What is a downstream collector?**  
It is the collector applied to the values within each group, such as counting, mapping, summingInt, or collecting to a Set.

**Q5. Why does reducing need an associative operation for parallel use?**  
The stream may split input and combine partial results in a different grouping. Associativity ensures the result is independent of grouping.

## 11. Practice problems

1. Count employees by department.
2. Find average salary by department.
3. Return unique employee names by department.
4. Partition numbers into even and odd.
5. Convert a list of orders into customerId -> totalAmount, handling repeated customers.
6. Find the highest-paid employee in each department.

## 12. Quick revision

groupingBy = many groups. partitioningBy = true/false. Downstream collectors calculate each group's result. toMap needs a merge function when keys may repeat.
