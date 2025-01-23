---
{"aliases":null,"tags":["leetcode/setup"],"projects":null,"url":null,"type":"Guide","Description":"How to setup leetcode API locally","Areas":"HowTos","publish":true,"date created":"2025-01-14T15:25","date modified":"2025-01-15T21:34","PassFrontmatter":true,"created":"2025-01-15T21:34:51.689+05:30","updated":"2025-01-15T21:34:51.689+05:30"}
---

# Setup Details
### References
- [GitHub - zimmshane/leetcode-puller-obsidian: A script for populating Leetcode question data into Obsidian using the QuickAdd.](https://github.com/zimmshane/leetcode-puller-obsidian)
- [GitHub - alfaarghya/alfa-leetcode-api: It's a custom leetcode api. This API provides endpoints to retrieve details about a user's profile, badges, solved questions, contest details, contest history, submissions, calendar and and also daily questions, selected problem, list of problems.](https://github.com/alfaarghya/alfa-leetcode-api)
### Commands to Startup Leetcode Api Locally

```shell
wsl #to enter wsl mode
nohup docker run -p 3000:3000 alfaarghya/alfa-leetcode-api:2.0.1 &
```

### Running it on Dedicated Machine
```
sudo docker run -d --restart unless-stopped -p 3005:3000 alfa-leetcode-api:latest
```
