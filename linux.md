# Add user to a group

```bash
sudo usermod -a -G groupname username
```

# ls -l

```
$ ls -l
-rw -r- -r--  1 slerest slerest   127 Jul 26 01:57 digital_garden.md
 |   |   |        |       |        |
 v   v   v        v       v        v
 1   2   3        4       5        6
```

- 1: permission owner
- 2: permission group
- 3: permission other 
- 4: owner group 
- 5: owner user 
- 6: size in byte 

# Chmod permission




| permission |  	rwx | 	value 8b |  	 value binary | 
|-------|----------------------|-------------|--------------------|
| None |  	--- |  	0 |  	000 |
| execute |  	--x |  	1 |  	001 |
| write |  	-w- |  	2 | 	010 |
| write and execute | 	-wx |  	3 | 	011 |
| read |  	r-- |  	4 |  	100 |
| read and execute |	r-x |  	5 |  	101 |
| read and write | rw-  |	6 |  	110 |
| all permissions |  	rwx |  	7 |  	111 | 
