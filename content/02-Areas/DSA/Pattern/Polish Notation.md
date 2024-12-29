---
{"tags":null,"projects":[null],"type":null,"Description":null,"Areas":null,"publish":true,"PassFrontmatter":true,"created":"2024-12-21T08:22:54.187+05:30","updated":"2024-12-26T09:37:58.221+05:30"}
---

# Reverse polish notation evaluation
- There are a lot of ambiguities with Infix notation. i.e., People aren't fully aware of how to use BODMAS/PEDMAS. 
	- This leads to those social media posts where different people have different solutions for one large arithmetic problem. 
	- e.g., `5 * 4 + 9 - 2 / 3 + 1 = ?`  the actual solution is `29.33` 
- Reverse polish notation has less ambiguities.
	-  e.g., `12 7 -`  = `5` & not `-5`
		- Remember the ordering