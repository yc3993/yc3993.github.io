---
title: MIT-Missing-Semester
categories: [Tech, General]
tags: [General]
render_with_liquid: false
---

## Lecture 1
#### The Shell Handout
Bourne Again SHell常用指令：
 - ls - l
 - cd
 - pwd
 - mkdir, touch 
 - rm, rm - r
 - cp:  
  `cp file.txt new_location/file.txt`
 - mv
 - cat 
 - grep   
  use case:  
    - `curl --head yutongc123.com | grep -i content-length | cut --delimiter=' ' -f2` (搜索content-length位置并按照‘ ’分隔取第二列)
    - grep -r "keyword" directory/ 
    - grep -E "pattern" file.txt 正则
  - chmod
  - echo
  - man
  - find  
  在指定目录下搜索文件或目录
  - tar:  
    `tar -cvf archive.tar files/`
  - wget:  
    `wget https://example.com/file.txt`
  - ssh

#### Exercises
1. Create a new directory called missing under /tmp.  
`mkdir /tmp/missing`

2. Look up the touch program. The man program is your friend.  
`man touch`

3. Use touch to create a new file called semester in missing.  
`touch /tmp/missing/semester`

4. Write the following into that file, one line at a time:
#!/bin/sh
curl --head --silent https://missing.csail.mit.edu
The first line might be tricky to get working. It’s helpful to know that # starts a comment in Bash, and ! has a special meaning even within double-quoted (") strings. Bash treats single-quoted strings (') differently: they will do the trick in this case. See the Bash quoting manual page for more information.  
`echo '#!/bin/bash' > semester`  
`echo "curl --head --silent https://missing.csail.mit.edu" >> semester`

5. Try to execute the file, i.e. type the path to the script (./semester) into your shell and press enter. Understand why it doesn’t work by consulting the output of ls (hint: look at the permission bits of the file).  
```bash
./semester
```
terminal returns the following error.  
```bash
zsh: permission denied: ./semester
```

6. Run the command by explicitly starting the `sh` interpreter, and giving it
   the file `semester` as the first argument, i.e. `sh semester`. Why does
   this work, while `./semester` didn't?  
   用户有使用`sh`指令执行函数的权限

7. Look up the `chmod` program (e.g. use `man chmod`).  
`man chmod` or `tldr chmod`  

8. Use `chmod` to make it possible to run the command `./semester` rather than
   having to type `sh semester`. How does your shell know that the file is
   supposed to be interpreted using `sh`? See this page on the
   [shebang](<https://en.wikipedia.org/wiki/Shebang_(Unix)>) line for more
   information.  
   We can use `chmod +x semester` to tackle the problem.  

9. Use `|` and `>` to write the "last modified" date output by
   `semester` into a file called `last-modified.txt` in your home
   directory.  
```bash
./semester | grep -i last-modified | cut -d' ' -f2- > last-modified.txt
```

