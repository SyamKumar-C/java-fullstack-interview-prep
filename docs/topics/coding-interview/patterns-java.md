# Java Coding Interview Patterns

> Priority: P1 | Level: Intermediate | Study time: 60–90 minutes

## 1. How to explain a coding solution
For every problem:
1. Restate input, output, and constraints.
2. Ask about duplicates, null/empty input, ordering, and input size.
3. Give a simple baseline approach.
4. Improve time/space complexity.
5. Explain the invariant or data structure.
6. Walk through an example.
7. Code, test edge cases, and state complexity.

Do not jump directly into code. Interviewers want to see how you reason.

## 2. HashMap frequency counting
Use a map when you need counts, membership, or a relationship between values.

```java
Map<Integer, Long> counts = numbers.stream()
    .collect(Collectors.groupingBy(n -> n, Collectors.counting()));
```

Typical problems: duplicates, first non-repeating character, two sum, anagrams, frequency sorting.

## 3. Two pointers
Use two indices that move through an array/string. Common when input is sorted or when comparing both ends.

```java
boolean isPalindrome(String value) {
    int left = 0, right = value.length() - 1;
    while (left < right) {
        if (value.charAt(left++) != value.charAt(right--)) return false;
    }
    return true;
}
```

Typical problems: palindrome, pair sum in sorted array, remove duplicates in-place, reverse array.

## 4. Sliding window
Maintain a range and update it as the right edge moves. Use for contiguous subarray/substring problems.

```java
int maxSumOfSizeK(int[] a, int k) {
    if (k <= 0 || k > a.length) throw new IllegalArgumentException();
    int sum = 0;
    for (int i = 0; i < k; i++) sum += a[i];
    int best = sum;
    for (int right = k; right < a.length; right++) {
        sum += a[right] - a[right - k];
        best = Math.max(best, sum);
    }
    return best;
}
```

This fixed-size window is O(n) time and O(1) extra space.

## 5. Stack and queue
Stack is useful for nested structure and last-in-first-out behavior: balanced brackets, next greater element, monotonic stack. Queue supports breadth-first search and level-order traversal.

Use ArrayDeque for stack/queue operations in most Java application code rather than legacy Stack.

## 6. Binary search
Use binary search on sorted data or on a monotonic answer space. Define whether the search is for an exact value, first true, or last true. Be careful with loop boundaries and integer overflow.

```java
int lowerBound(int[] a, int target) {
    int low = 0, high = a.length;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (a[mid] < target) low = mid + 1;
        else high = mid;
    }
    return low;
}
```

Returns the first index whose value is at least target, or array length.

## 7. Sorting and intervals
Sort intervals by start or end, then scan once. Explain whether touching intervals overlap in the problem's definition.

## 8. Trees and graphs
- DFS: recursion or explicit stack.
- BFS: queue; useful for shortest path in an unweighted graph.
- Visited set: prevents repeated traversal and cycles.
- Tree recursion: define base case and combine left/right results.

## 9. Complexity quick guide
| Pattern | Typical time |
|---|---|
| One pass | O(n) |
| Nested full loops | O(n²) |
| Sort then scan | O(n log n) |
| Hash lookup average | O(1) |
| Binary search | O(log n) |
| BFS/DFS | O(V + E) |

State assumptions; hash operations are expected O(1), not a universal worst-case guarantee.

## 10. Practice set
1. Find duplicate values and their frequencies.
2. Find the first non-repeating character.
3. Two Sum.
4. Check balanced brackets.
5. Longest substring without repeating characters.
6. Maximum sum subarray of size K.
7. Merge overlapping intervals.
8. Binary search first occurrence.
9. Reverse a linked list.
10. Level-order traversal of a binary tree.

For each, write brute force first, optimize, test empty/singleton/duplicate/boundary inputs, and state time and space complexity.
