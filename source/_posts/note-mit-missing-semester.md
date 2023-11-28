---
title: MIT Missing Semester 学习笔记
date: 2023-9-3 20:04:28
tags:
  - 笔记
  - 命令行
  - Git
categories: [笔记]
---

## Shell

### 基本使用相关

提示符：`USERNAME:~$`。当前工作目录为 `~`，即主目录 home。`$` 意味着当前不为 root。

shell 解析命令的时候以空格为分隔，第一个子段指向程序，之后的分别是程序的参数。

### 导航

`/` 为根目录（Linux 下），`.` 为当前目录，`..` 为父目录。

`pwd` 显示当前路径，`ls` 列出当前目录下的内容。

`ls` 的 `-l` 表示长列表模式。

```bash
Taoyu Yang@YANGTY-LAPTOP MINGW64 ~/Desktop
$ ls -l
total 5
-rw-r--r-- 1 Taoyu Yang 197121 1112  8月  7 17:50 'clash config.txt'
-rw-r--r-- 1 Taoyu Yang 197121  282  7月 28 21:06  desktop.ini
drwxr-xr-x 1 Taoyu Yang 197121    0  8月 27 22:39  testFolder/
```

d 表示是否为路径。

九个字母，三个一组，分别描述 owner，users 和 everyone 对其的权限。

rwx 分别是 read write 和 excute。

`mv` 移动，`cp` 复制，`mkdir` 新建文件夹。

`man xxx` 表示查看 `xxx` 的 manual。

### 重定向与管道初步

最简单的重定向就是 `< inputfile` 和 `> outputfile`。

`>> outputfile` 是在文末追加。

`|` 可以管道。

```bash
missing:~$ ls -l / | tail -n1
```

管道是将前者的输出（可以理解为 stdout)

## Shell Scripting

直接可以定义`foo=bar`，但是不能有空格，注意空格是拿来分开参数的。

调用变量的时候用美元符号。

字符串单引号不会展开美元符号，双引号会展开为变量

bash 的函数参数：

```bash
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```

Here `$1` is the first argument to the script/function. Unlike other scripting languages, bash uses a variety of special variables to refer to arguments, error codes, and other relevant variables. Below is a list of some of them. A more comprehensive list can be found [here](https://tldp.org/LDP/abs/html/special-chars.html).

- `$0` - Name of the script

- `$1` to `$9` - Arguments to the script. `$1` is the first argument and so on. 其实就很类似于 `argv[1]`

- `$@` - All the arguments 会展开为所有参数

- `$#` - Number of arguments

- `$?` - 上一条命令的返回值

  true 的错误代码始终为 0，false 的错误代码始终为 1。

  请注意，这里和一般的 true 和 false 是不一样的。

  `false || echo "Oops fail"`。

- `$$` - Process identification number (PID) for the current script

- `!!` - 上一次命令. A common pattern is to execute a command only for it to fail due to missing permissions; 常用 `sudo !!`

- `$_` - Last argument from the last command. If you are in an interactive shell, you can also quickly get this value by typing `Esc` followed by `.` or `Alt+.`

可以使用 `;` 来分隔变量。

`foo=$(pwd)`，`echo "We are in $(pwd)"`。*command substitution*，用一个命令的输出作为一个变量。

*process substitution*：`<(CMD)` 会执行里面的命令，然后将输出放入一个临时文件，同时将 `<()` 替换成文件的名字。一个比较常见的会是 `diff <(ls foo) <(ls bar)`，可以拿来比较 foo 和 bar 目录中的内容差异。

```bash
#!/bin/bash

echo "Starting program at $(date)" # Date will be substituted

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # When pattern is not found, grep has exit status 1
    # We redirect STDOUT and STDERR to a null register since we do not care about them
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

`2>` 是指向 `STDERR` 流的。**使用 `man test` 可以查询那一堆堆比较运算符**。

一些很抽象的展开：通配符，花括号：

```bash
convert image.{png,jpg}
# Will expand to
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# Will expand to
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# Globbing techniques can also be combined
mv *{.py,.sh} folder
# Will move all *.py and *.sh files


mkdir foo bar
# This creates files foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h
touch {foo,bar}/{a..h}
touch foo/x bar/y
# Show differences between files in foo and bar
diff <(ls foo) <(ls bar)
# Outputs
# < x
# ---
# > y
```

Shebang：指导选择哪个脚本的解释器。

## Some Shell Tools

查看手册：`man`，但可能麻烦。实例：`tldr`。

查找文件：

```bash
# Find all directories named src
find . -name src -type d
# Find all python files that have a folder named test in their path
find . -path '*/test/*.py' -type f
# Find all files modified in the last day
find . -mtime -1
# Find all zip files with size in range 500k to 10M
find . -size +500k -size -10M -name '*.tar.gz'
# Delete all files with .tmp extension
find . -name '*.tmp' -exec rm {} \;
# Find all PNG files and convert them to JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

还有一个是 `fd`，语法更好用一些。

`locate` 基于 db 索引，会快些，`updatedb` 更新索引。

查找文件内容：

`grep` 是最基本的。`-C` 查找上下文，如 `grep -C 5`。`ack`，`ag` 和 `rg` 是改进版本。

```bash
# Find all python files where I used the requests library
rg -t py 'import requests'
# Find all files (including hidden files) without a shebang line
rg -u --files-without-match "^#\!"
# Find all matches of foo and print the following 5 lines
rg foo -A 5
# Print statistics of matches (# of matched lines and files )
rg --stats PATTERN
```

