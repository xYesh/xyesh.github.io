---
{"aliases":null,"tags":["cli-commands","cli-commands/linux","laptopsetup"],"projects":null,"url":null,"type":null,"Description":"Useful cli / command line commands that are useful & Things to do to setup a new laptop","Areas":"HowTos","publish":true,"date created":"2025-01-13T23:25","date modified":"2025-02-06T13:11","PassFrontmatter":true,"created":"2025-01-14T15:25:42.520+05:30","updated":"2025-02-06T13:11:33.009+05:30"}
---

## Files and Folder

### Unique Lines in a File

```Plain
sort file.log | uniq -u
```

### Grep Commands

```Plain
grep -inr "Text" folder/to/be/searched/
```

### Find File Location

```Plain
find . -name 'log4j*'
```

### List Files in Folder and Sort Them Based on Size

```Plain
df -h / | sort
```

## Processes

### Pid Running Location

```Plain
lsof -p <PID> | grep cwd
```

### Who is Occupying Port

```Plain
sudo ss -lptn 'sport = :6379'

lsof -nP -i4TCP:50070 | grep LISTEN
```

### All Ports in Use

```Plain
sudo lsof -i -P -n | grep LISTEN
```

### SSH with Port Forwarding

```Plain
ssh -L 60010:127.0.0.1:60010 10.53.115.88
ssh -N -L localhost:50070:10.33.82.176:50070 10.33.82.176
```

### Kill All Instances of a Program

```Plain
ps -C fusuma
pkill fusuma
```

### Find and Kill Hung Processes

```Plain
linux hung process PID kill - zombie process

ps axo stat,ppid,pid,comm | grep -w defunct
```

### Release All buff/cache RAM

```Lua
sync && sudo sysctl -w vm.drop_caches=3 && sudo sysctl -w vm.drop_caches=0
```

  

## Disk Commands

### Disk Space Commands

```Plain
df -h (shows free space in entire disk)
```

## Shortcuts

```Plain
1. Ctrl+a Move cursor to start of line
2. Ctrl+e Move cursor to end of line
3. Ctrl+b Move back one character
4. Alt+b Move back one word
5. Ctrl+f Move forward one character
6. Alt+f Move forward one word
7. Ctrl+d Delete current character
8. Ctrl+w Cut the last word
9. Ctrl+k Cut everything after the cursor
10. Alt+d Cut word after the cursor
11. Alt+w Cut word before the cursor
12. Ctrl+y Paste the last deleted command
13. Ctrl+_ Undo
14. Ctrl+u Cut everything before the cursor
15. Ctrl+xx Toggle between first and current position
16. Ctrl+l Clear the terminal
17. Ctrl+c Cancel the command
18. Ctrl+r Search command in history - type the search term
19. Ctrl+j End the search at current history entry
20. Ctrl+g Cancel the search and restore original line
21. Ctrl+n Next command from the History
22. Ctrl+p previous command from the History
```

## Network

### Telnet Nc

```Plain
nc -v -w 2 -z 172.18.241.23 6388
telnet <ip> <port>
```

## Vim Commands

### Paste into Vim

```Plain
:set paste
```

### Delete All Contents in a File / Clear a File

```Plain
Esc + gg + dG
```

### Search in File

```Plain
:%s/pattern//n
```

### Shortcuts

```Plain
dd - delete current line
```

## Tmux

[cheat sheet](https://tmuxcheatsheet.com/)  

### Install Tmux

```Plain
apt install tmux
apt install locales-all
```
