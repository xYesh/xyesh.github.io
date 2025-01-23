---
{"aliases":null,"tags":["leetcode/graph","leetcode/kahnsAlgorithm"],"projects":null,"url":null,"type":null,"Description":null,"Areas":null,"publish":true,"date created":"2025-01-14T15:25","date modified":"2025-01-14T17:29","PassFrontmatter":true,"created":"2025-01-14T17:29:51.049+05:30","updated":"2025-01-14T17:29:51.049+05:30"}
---


# Algorithm Basics
- Calculate the number of `in-degree` and `out-degree` for each node and then go through them one by one and resolve the dependencies.
![Pasted image 20241230094031.png](../../../02-Areas/DSA/Algorithms/attachments/Pasted%20image%2020241230094031.png)
- If we find a cycle, There is no `Topological` order. e.g.,
 ![Pasted image 20241230094348.png](../../../02-Areas/DSA/Algorithms/attachments/Pasted%20image%2020241230094348.png)

# Relevant Problems
### [[02-Areas/DSA/Leetcode/Medium/207. Course Schedule\|207. Course Schedule]] Pseudo Code
- Basically there should be some course with no pre-requisites. That is the only way that we can solve the problem.
- We add all courses with no pre-requisites to the queue.
- we remove the indegree from the courses that depend on them and then see if they have become independent courses with no pre-requisites.
- We keep doing this until the queue is empty.
- If we've visited all the nodes, there is no cycle.
 
