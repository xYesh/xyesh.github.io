---
{"tags":["leetcode/setup"],"projects":null,"url":null,"type":"Guide","Description":"How to setup leetcode API locally","Areas":"HowTos","publish":true,"PassFrontmatter":true,"created":"2024-12-30T01:46:04.000+05:30","updated":"2024-12-30T01:46:04.000+05:30"}
---

# Setup details
### References 
-  [GitHub - zimmshane/leetcode-puller-obsidian: A script for populating Leetcode question data into Obsidian using the QuickAdd.](https://github.com/zimmshane/leetcode-puller-obsidian)
- [GitHub - alfaarghya/alfa-leetcode-api: It's a custom leetcode api. This API provides endpoints to retrieve details about a user's profile, badges, solved questions, contest details, contest history, submissions, calendar and and also daily questions, selected problem, list of problems.](https://github.com/alfaarghya/alfa-leetcode-api)
### Commands to startup leetcode api locally

```shell
wsl #to enter wsl mode
nohup docker run -p 3000:3000 alfaarghya/alfa-leetcode-api:2.0.1 &
```
