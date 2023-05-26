---
title: MIT Missing Semester
categories: [Tech, General]
tags: [General]
render_with_liquid: true
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


## Lecture 2
#### Shell Scripting Handout 
Shell Scripting Type:  
1. 获取和自定义参数  
  - $0 脚本名
  - $1 - 9 参数名  
  && 和 || 

    ```bash
    false || echo "Oops, fail"
    # Oops, fail

    true || echo "Will not be printed"
    #

    true && echo "Things went well"
    # Things went well

    false && echo "Will not be printed"
    #
    ```

2. 使用命令结果作为参数  
  - 先执行date再执行echo: `echo "Starting program at $(date)"`  
  - 将foo和bar中数据输出到一个临时文件中再比较区别: `diff <(ls foo) <(ls bar)`  
    这段脚本会遍历我们提供的参数，使用grep 搜索字符串 foobar，如果没有找到，则将其作为注释追加到文件中。

    ```bash
    #!/bin/bash

    echo "Starting program at $(date)" # date会被替换成日期和时间

    echo "Running program $0 with $# arguments with pid $$"

    for file in "$@"; do
        grep foobar "$file" > /dev/null 2> /dev/null
        # 如果模式没有找到，则grep退出状态为 1
        # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
        if [[ $? -ne 0 ]]; then
            echo "File $file does not have any foobar, adding one"
            echo "# foobar" >> "$file"
        fi
    done
    ```

3. 通配符  
  ?和*匹配多种字符：  
  ```cp /path/to/project/{foo,bar,baz}.sh /newpath```   
  ```mv *{.py,.sh} folder```

4. Python编写脚本  
  #!/usr/local/bin/python
    ```bash
    #!/usr/local/bin/python
    import sys
    for arg in reversed(sys.argv[1:]):
        print(arg)
    ```

#### Shell Tools  
1. 查找文件 find, fd   
  ```bash
  # 查找所有名称为src的文件夹
  find . -name src -type d // type: dir
  # 查找所有文件夹路径中包含test的python文件
  find . -path '*/test/*.py' -type f
  # 查找前一天修改的所有文件
  find . -mtime -1
  # 查找所有大小在500k至10M的tar.gz文件
  find . -size +500k -size -10M -name '*.tar.gz'
  ```

  finding and executing pipelines
  ```bash
  # Delete all files with .tmp extension
  find . -name '*.tmp' -exec rm {} \;
  # Find all PNG files and convert them to JPG
  find . -name '*.png' -exec convert {} {}.jpg \;
  ```

2. 查找字段 grep  

  
