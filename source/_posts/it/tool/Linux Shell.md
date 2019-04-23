---
title: Linux Shell
date: 2018-8-6 20:25:00
tags: [Linux,Shell]
comments: true
---


### SET 命令

用途：

可以设置 shell 的执行方式，不带参数时输出环境变量。

```shell
> set [+-abCdefhHklmnpPtuvx]
```

注：

   	1. [-] 表示设置参数
   	2. [+] 表示取消设置参数

示例：

```shell
> tmp="nice day"
> $tmp
nice day
> set | grep tmp
tmp="nice day"
> unset tmp
> $tmp

```

### SSH 命令

用途：

连接远程计算机。

```shell
> ssh  -p <port> <user>@<hostname> <remote cmd>
```

注：

1. [-p] 指定端口号，默认为 22
2. [remote cmd] 远程执行命令并显示到本地继续工作

配置 [.ssh] 在 [~/.ssh/config] 中，

```
Host <myhost>
User <username>
HostName <ip>
IdentityFile ~/env/<username>.id_rsa
```

可快速进行ssh连接，`ssh myhost` 。

### 进程管理命令

#### 前后台切换命令 bg、fg 

```shell
> fg <task id>
> bg <task id>
```

注：

1. 当使用这两个任务时，如果提示 `no job control in this shell` ，可以使用 `set -m` 命令开启。
2. 当一个任务进程在执行过程中想要暂时挂起，可以使用 `ctrl + Z` ，若要终止，进入 `suspended `状态，使用 `ctrl + C ` ，进入`terminated` 状态。
3. 挂起的任务可以通过 `fg` 重新调到前台运行，或者通过 `bg` 调到后台运行。
4. 若要终止挂起的任务，可以使用 `kill <pid>` 。

#### & 字符的作用

用途：

放在启动参数后面表示设置此进程为后台进程，这是称之为 job。通过 `jobs`

命令可以查看当前有多少后台运行的命令。

示例：

```shell
> ./run.sh &
> jobs -l
```

注：

1. [-l] 显示pid。

#### nohup 保住进程

当用户关闭窗口时，终端会受到HUP（hungup）信号，从而关闭其所有子进程。

若要让命令不被终止，有两种处理方式：

1. 让进程忽略HUP信号， `nohup` 之所为
2. 让进程运行在新的会话中，`setsid` 、`(<cmd> &)`之所为

示例：

```shell
> nohup ping www.ibm.com &
> setsid ping www.ibm.com
> (ping www.ibm.com &)
> ps -ef | grep www.ibm.com
```

如果事前没有使用前面的方式保住进程，那么可以使用如下流程进行补救：

1. 先 `ctrl+Z` 挂起进程，使用 `jobs -l` 获取进程号；
2. 使用 `bg <pid>` 将其放入后台运行；
3. 再使用 `disown <pid>` ，可以避免 HUP 信号的影响。

使用 `nohup` 命令时默认会将输出保存到文件 `nohup.ou` ，可以通过以下方式修改保存文件

```shell
> nohup ${my_app} > common.out 2> error.out
> nohup ${my_app} > all_in.out &
```

#### ps 进程快照

process status, 列出当前运行的进程的快照。命令参数：

1. [-A] 显示所有进程；
2. [-e] 显示环境变量；
3. [-f] 显示程序间的关系；
4. [-aux] 显示所有包含其他使用者的进程。

查找特定的名称的进程时通常与 grep 命令连用，以查找 Wechat 相关进程为例：

```shell
# 将所有进程中的包含 Wechat 的连同表头一起输出
> ps -ef | grep -Ei 'PID|Wechat'
```

在我的电脑上输出结果是：

```shel
  UID   PID  PPID   C STIME   TTY           TIME CMD
  501  1079     1   0 Thu10AM ??         3:23.55 /Applications/WeChat.app/Contents/MacOS/WeChat
```

#### kill 终止进程

终止进程的命令，参数有：

1. [-9] 强制删除；

### 文件管理命令

#### tail 查看文件末尾

```shell
> tail [-n <line numbers>] [-f] <file>
```

注：

1. [-n] 可以指定显示的日志行数，不指定默认为 10 行
2. [-f] 可以监视文件的增长，简缩的命令为 `tailf`
3. 与之相对应的显示前面几条日志的命令为 `head`

#### cat 一次显示文件内容

```shell
> cat [-ns] <file>...
```

注：

1. [-n] 可以显示行号
2. [-s] 可以合并连续的空行为一
3. 与之相对的命令为 `tac` 可以从尾到头显示文件内容（Mac 好像不支持）

示例：

```shell
# 一次显示整个文件
cat <file1> <file2>
# 创建新文件
cat > <file>
# 将多个文件合并为一个文件
cat <file1> <file2> > <file> 
```

#### more 逐页显示文件内容

```shell
> more [-s] [-<line numbers>] [+<line numbers>] <file>
```

注：

1. [-s] 可以合并连续的空行为一
2. [-n] 可以定义每次上滚 n 行
3. [+n] 可以指定从第 n 行开始显示
4. `ctrl +F` 向上滚动一屏，`空格键` 向下滚动一屏
5. `=` 显示当前显示的行数范围及文件名称

示例：

```shell
# 从第 100 行开始显示 debug.log， 每次滚动 10 行，合并空行为一
> more -s -10 +100 debug.log
```

#### less 分页显示文件内容

比 `more` 强大的真正意义上的分页查看文件命令，`more` 一次会加载整个文件，而 `less` 是逐页加载。`less` 还支持向上翻页和搜索。

```shell
> less [-gimNsS] [-x <numbers>] <file>
```

注：

1. [-g] 高亮先前的搜索结果
2. [-i] 搜索时忽略大小写
3. [-m] 显示已显示内容的百分比
4. [-N] 显示行号
5. [-s] 合并显示连续的空行为一
6. [-S] 行过长时舍弃超出部分
7. [-x 4] 设置`tab` 为规定的数量的空格
8. `/` 向上查找
9. `?` 向下查找
10. `n` 正向重复查找
11. `N` 反向重复查找
12. `b` 向后翻一页
13. `d` 向后翻半页
14. `y` 向前翻一页
15. `u` 向前翻半页
16. `q` 退出

示例：

```shell
# 查找历史命令中包含run的命令
> history | less
/run
# 假设找到要运行的命令序号为 100，按 q 退出，再按如下重新执行
> !100
```

#### grep 查找文件内容

grep (global search regular expression(RE) and print out the line，全面搜索正则表达式并把行打印出来)

```shell
> grep <option> <file>...
```

1. [-A num] 显示匹配行后面 num 行
2. [-B num] 显示匹配行前面 num 行
3. [-C num] 显示匹配行前后各 num 行
4. [--color=auto] 匹配结果自动配色
5. [-m num] 当匹配了 num 行后不再继续查找
6. [-n] 显示匹配行所在行号
7. [-v] --invert-match 输出不匹配的行
8. [-i] 不区分大小写
9. [-E] --extended-regexp  支持扩展的正则表达式

示例：

```shell
# 查询日志文件中包含 keyword 但不含 keywords 的行，最多输出 10 行匹配，且显示匹配行前后 2 行
> grep -C 2 -n -m 10 keyword *.log | grep -v keywords

# 同时查找多个关键字
> grep -E 'a_key|b_key' a.file
```

#### tar 解压缩文件

tar 最初的设计目的是将文件备份到磁带上（**t**ape **ar**chie），故名 tar。

tar 代表未压缩的 tar 文件，已经压缩的 tar 文件会附加压缩文件的扩展名，如 `.tar.gz` 表示经过 gzip 压缩。

命令参数：

1. [-x] --extract 解开 tar 文件
2. [-z] --gzip,--gunzip,--unzip 调用 gzip 执行压缩或解压缩
3. [-f] --file 指定要处理的文件名
4. [-t] --list 列出 tar 文件中包含的文件的信息

```shell
# 解压 .tar.gz 文件到当前文件夹
> tar -zxf a_file.tar.gz
# 解压 .tar 文件到 tmp 文件夹
> tar -xf b_file.tar -C tmp/
# gzip 压缩文件 c_file 和目录 d_dir 到 out.tar.gz
> tar -czvf out.tar.gz c_file d_dir
```

#### gunzip 解压文件

gzip -d all.gz

gunzip all.gz

#### awk 文本分析

文本处理语言和工具，用于在文件中查找与模式匹配的行并在这些行上执行指定的操作，名称取自三位创始人 Alfred **A**ho，Peter **W**einberger, 和 Brian **K**ernighan 的 Family Name 的首字符。可参见 [AWK程序设计语言](https://awk.readthedocs.io/en/latest/index.html) 、 [awk命令 - IBM](https://www.ibm.com/support/knowledgecenter/zh/ssw_aix_72/com.ibm.aix.cmds1/awk.htm)  以及 [Linux awk 命令 - runoob.com](http://www.runoob.com/linux/linux-comm-awk.html) 进行完整的学习。下面简单介绍下语法：

```shell
> awk '{[pattern] action}' {filenames}   # 行匹配语句 awk '' 只能用单引号
```

Pattern 支持：

- 正则表达式，eg. `awk '/smith?/' a_file`
- 关系表达式，eg. `awk '$1 > $3' b_file`
- 模式的组合，以上模式的组合
  - 与或非 `|| && !`，值为真则匹配
  - 圆括号 `()` ，组合匹配
  - 逗号 `,` ，匹配前一个的匹配
- BEGIN 和 END 模式
  - BEGIN 模式在读取输入前执行
  - END 模式在读取输入后执行

示例：

```shell
# 指定分隔符(默认是空格或 TAB)，有两种写法
## 用逗号分割
> awk -F, '{print $3}' a_file
> awk 'BEGIN{FS=","} {print $3}' a_file
## 先用空格分割再用逗号分割
> awk -F '[ ,]'  '{print $3}' a_file
> awk 'BEGIN{FS="[ ,]"} {print $3}' a_file
# 忽略匹配文本的大小写
> awk 'BEGIN{IGNORECASE=1} /a_key/' a_file
# 获取 Kafka 进程的 ID
> ps aux | grep server.properties | grep -v grep | awk '{print $2}'
```

### alias 添加别名

树状查看文件：

```shell
# 注意：这种方式会列出所有文件，建议使用 brew instal tree. 因为可以使用 `tree -L [depth]`自定义深度
> alias tree="find . -print | sed -e 's;[^/]*/;|____;g;s;____|; |;g'"
> source ~/.zshrc
```

取消别名：

```shell
> unalias tree
```

### ctrl 移动光标

| Windows 快捷键     | Mac 快捷键      | 功能                                                         |
| ------------------ | --------------- | ------------------------------------------------------------ |
| ctrl + 左右键      | option + 左右键 | 在单词之间跳转                                               |
| ctrl + a           | 同左            | 跳到本行的行首                                               |
| ctrl + e           | 同左            | 跳到页尾                                                     |
| ctrl + u           | 同左            | 删除当前光标前面的文字                                       |
| ctrl + k           | 同左            | 删除当前光标后面的文字                                       |
| ctrl + w、ctrl + d | 同左            | 对于当前的单词进行删除操作，w删除光标前面的单词的字符，d则删除后面的字符 |
| ctrl + y           | 同左            | 恢复删除的文字                                               |
| ctrl + l           | 同左            | 清屏                                                         |

### 同步一台服务器中的文件到另一台

基本思路：拷贝文件到本地，然后覆盖另一台上的同名文件。命令如下，其中 `server-a` 以及 `server-b` 是定义在文件 `~/.ssh/config` 中的 `HOST` 。

```shell
ssh server-a "cat ~/test.txt" > ~/Desktop/test.txt;scp ~/Desktop/run.sh server-b:~/test.txt
```

### Tree 命令中文乱码

```shell
tree -N folder
```

### 更改文件访问权限

> 以下摘引自 [鸟哥的 Linux 私房菜 第六章](http://cn.linux.vbird.org/linux_basic/0210filepermission.php)，略有删减。

（1）**数字类型改变文件权限**

Linux文件的基本权限就有九个，分别是 `owner/group/others` 三种身份各有自己的 `read/write/execute` 权限，文件的权限字符为：`-rwxrwxrwx`， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

> r:4
> w:2
> x:1

每种身份( `owner/group/others` )各自的三个权限( `r/w/x` )分数是需要累加的，例如当权限为：` [-rwxrwx---]`  分数则是

> owner = rwx = 4+2+1 = 7
> group = rwx = 4+2+1 = 7
> others= --- = 0+0+0 = 0

变更权限的指令chmod的语法是这样的：

```shell
chmod [-R] xyz 文件或目录
```

选项与参数：

- xyz : 就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。
- -R : 进行递归(recursive)的持续变更，亦即连同次目录下的所有文件都会变更。

（2）**符号类型改变文件权限**

还有一个改变权限的方法呦！从之前的介绍中我们可以发现，基本上就九个权限分别是 `(1)user (2)group (3)others `三种身份啦！那么我们就可以藉由 `u, g, o`来代表三种身份的权限！此外， `a`则代表 all 亦即全部的身份！那么读写的权限就可以写成 `r, w, x`！也就是可以使用底下的方式来看：

| 格式 | chmod | u g o a | +(加入) -(除去) =(设定) | r w x | 文件或目录 |
| ---- | ----- | ------- | ----------------------- | ----- | ---------- |
| 例子 | chmod | a       | =                       | r     | .bashrc    |

来实作一下吧！假如我们要设定一个文件的权限成为`-rwxr-xr-x`时，基本上就是：

- user (u)：具有可读、可写、可执行的权限；
- group 与 others (g/o)：具有可读与执行的权限。

```shell
chmod  u=rwx,go=rx  .bashrc
# 注意喔！那个 u=rwx,go=rx 是连在一起的，中间并没有任何空格！
chmod  a+w  .bashrc
# 增加.bashrc这个文件的每个人均可写入的权限
```

### 正则方式删除文件（Delete files with regular expression）

偶然间，使用 `ll` 命令发现桌面上有许多 `~$` 开头的文件，是用微软三件套打开后暂存文件，原文件已经删除了，可这暂存文件还隐存在桌面上，于是想要批量删除它们。

```shell
# 在当前目录找到以 “~$” 开头的文件，然后执行删除操作
find . -name "~\$*" -delete
```

### wget 下载文件

```shell
wget "http://mirrors.hust.edu.cn/apache/kafka/2.1.0/kafka_2.11-2.1.0.tgz"
```

### 查看系统信息

```shell
# 查看内核/操作系统/CPU信息
uname -a 
# 查看操作系统版本
head -n 1 /etc/issue 
# 查看CPU信息
cat /proc/cpuinfo 
# 查看计算机名
hostname 
# 查看环境变量
env 
# 查看内存使用量和交换区使用量
free -m 
# 查看各分区使用情况
df -h 
# 查看指定目录的大小
du -sh <目录名> 
# 查看内存总量
grep MemTotal /proc/meminfo 
# 查看空闲内存量
grep MemFree /proc/meminfo 
# 查看系统运行时间、用户数、负载
uptime 
# 查看系统负载
cat /proc/loadavg 
# 查看所有磁盘分区
fdisk -l 
```





