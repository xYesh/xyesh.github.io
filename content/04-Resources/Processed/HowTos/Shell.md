---
{"tags":["cli-commands","cli-commands/linux","laptopsetup"],"projects":null,"url":null,"type":null,"Description":"Useful cli / command line commands that are useful & Things to do to setup a new laptop","Areas":"HowTos","publish":true,"PassFrontmatter":true,"created":"2025-01-13T23:25:30.000+05:30","updated":"2025-01-13T23:25:30.000+05:30"}
---

## Files and folder

### unique lines in a file

```Plain
sort file.log | uniq -u
```

### grep commands

```Plain
grep -inr "Text" folder/to/be/searched/
```

### find file location

```Plain
find . -name 'log4j*'
```

### list files in folder and sort them based on size

```Plain
df -h / | sort
```

## Processes

### pid running location

```Plain
lsof -p <PID> | grep cwd
```

### who is occupying port

```Plain
sudo ss -lptn 'sport = :6379'

lsof -nP -i4TCP:50070 | grep LISTEN
```

### all ports in use

```Plain
sudo lsof -i -P -n | grep LISTEN
```

### SSH with port forwarding

```Plain
ssh -L 60010:127.0.0.1:60010 10.53.115.88
ssh -N -L localhost:50070:10.33.82.176:50070 10.33.82.176
```

### kill all instances of a program

```Plain
ps -C fusuma
pkill fusuma
```

### find and kill hung processes

```Plain
linux hung process PID kill - zombie process

ps axo stat,ppid,pid,comm | grep -w defunct
```

### Release all buff/cache RAM

```Lua
sync && sudo sysctl -w vm.drop_caches=3 && sudo sysctl -w vm.drop_caches=0
```

  

## Disk commands

### disk space commands

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

### telnet nc

```Plain
nc -v -w 2 -z 172.18.241.23 6388
telnet <ip> <port>
```

## Vim commands

### paste into vim

```Plain
:set paste
```

### Delete all contents in a file / clear a file

```Plain
Esc + gg + dG
```

### search in file

```Plain
:%s/pattern//n
```

### shortcuts

```Plain
dd - delete current line
```

## Tmux

[cheat sheet](https://tmuxcheatsheet.com/)  
### install tmux  

```Plain
apt install tmux
apt install locales-all
```