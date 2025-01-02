---
{"tags":["leetcode/graph","leetcode/kahnsAlgorithm"],"projects":null,"url":null,"type":null,"Description":null,"Areas":null,"publish":true,"PassFrontmatter":true,"created":"2024-12-30T09:37:31.603+05:30","updated":"2024-12-30T09:57:43.808+05:30"}
---


# Algorithm basics
- Calculate the number of `in-degree` and `out-degree` for each node and then go through them one by one and resolve the dependencies. 
![Pasted image 20241230094031.png](../../../02-Areas/DSA/Algorithms/attachments/Pasted%20image%2020241230094031.png)
- If we find a cycle, There is no `Topological` order. e.g.,
 ![Pasted image 20241230094348.png](../../../02-Areas/DSA/Algorithms/attachments/Pasted%20image%2020241230094348.png)

# Relevant problems
### [[02-Areas/DSA/Leetcode/Medium/207. Course Schedule\|207. Course Schedule]] pseudo code
- Basically there should be some course with no pre-requisites. That is the only way that we can solve the problem.
- We add all courses with no pre-requisites to the queue. 
- we remove the indegree from the courses that depend on them and then see if they have become independent courses with no pre-requisites. 
- We keep doing this until the queue is empty. 
- If we've visited all the nodes, there is no cycle.
 
