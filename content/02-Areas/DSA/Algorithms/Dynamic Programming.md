---
{"aliases":["#leetcode/dynamic-programming","#leetcode/memoization"],"tags":null,"publish":true,"date created":"Wednesday, December 4th 2024, 9:55:14 pm","date modified":"Thursday, January 2nd 2025, 8:58:23 am","PassFrontmatter":true,"created":"2024-12-04T21:55:14.776+05:30","updated":"2025-01-02T09:42:10.606+05:30"}
---


# Bottom-Up dynamic programming (Tabulation)
## Algorithm for [[02-Areas/DSA/Leetcode/Easy/746. Min Cost Climbing Stairs\|746. Min Cost Climbing Stairs]]
1. Define an array `minimumCost`, where `minimumCost[i]` represents the minimum cost of reaching the ith step. The array should be one element longer than `costs` and start with all elements set to `0`.
    
    - The reason the array should contain one additional element is because we will treat the _top floor_ as the _step_ to reach.
2. Iterate over the array starting at the 2nd index. The problem statement says we are allowed to start at the 0th or 1st step, so we know the minimum cost to reach those steps is `0`.
    
3. For each step, apply the recurrence relation - `minimumCost[i] = min(minimumCost[i - 1] + cost[i - 1], minimumCost[i - 2] + cost[i - 2])`. As you can see, as we populate `minimumCost`, it becomes possible to solve future subproblems. For example, before solving the 5th and 6th steps we are required to solve the 4th step.
    
4. At the end, return the final element of `minimumCost`. Remember, we are treating this "step" as the top floor that we need to reach.

# Find all possible combinations.
## Algorithm
- Return `1` when the <span style="background:rgba(74, 82, 199, 0.2)">success criteria is met</span>.
- recursively call the helper function and add the results of the various calls.
	- Add a memoization check to the beginning of the function to improve time complexity.
## Problems
- [[02-Areas/DSA/Leetcode/Easy/70. Climbing Stairs\|70. Climbing Stairs]]

# Find the best combination or combination with some conditions
## Algorithm
- 
## Problems
- [[02-Areas/DSA/Leetcode/Easy/746. Min Cost Climbing Stairs\|746. Min Cost Climbing Stairs]]

# Kadane's Algorithm
## Resources
- [Kadane's Algorithm to Maximum Sum Subarray Problem - YouTube](https://www.youtube.com/watch?v=86CQq3pKSUw)
## Algorithm
- At each index we try to determine the largest sum till that index.
- The largest sub array ending at the n<sup>th</sup> index is <font color="#4f6128">either the current element alone or prev sum + curr element.</font>
- There is<font color="#953734"> no two pointer </font>here
## Problems
- [[02-Areas/DSA/Leetcode/Medium/53. Maximum Subarray\|53. Maximum Subarray]]
