---
{"publish":true,"PassFrontmatter":true,"created":"2025-01-14T15:25:43.310+05:30","updated":"2024-12-26T15:07:58.000+05:30"}
---


# Declaration

#leetcode/lamda

```java
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            // Sort by the first element ascending
	if (a[0] != b[0]) {
		return Integer.compare(a[0], b[0]);
	}
	// If first elements are equal, sort by the second element descending
	return Integer.compare(b[1], a[1]);
});
```

