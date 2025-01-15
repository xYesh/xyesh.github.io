---
{"aliases":null,"tags":null,"publish":true,"date created":"2025-01-14T15:25","date modified":"2025-01-14T17:29","PassFrontmatter":true,"created":"2025-01-14T17:29:46.067+05:30","updated":"2025-01-14T17:29:46.067+05:30"}
---


# Just Have a Validation Function & Directions Array

```java
int[][] directions = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};

public boolean valid(int row, int col) {
	return 0 <= row && row < m && 0 <= col && col < n;
}
```

# Finding Gates or Closest Value Type Questions
- It makes sense to look at the question ==backwards==.
	- i.e., look from the gate and calculate the closest steps. don't look from every path for the closest gate.
