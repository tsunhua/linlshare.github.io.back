---
title: Vim 小记
date: 2018-9-5 19:42:00
tags: [Vim,编辑器]
comments: true
---


### 1. Vim 剪贴板如何与系统剪贴板交互？

`“+y ` 把选中内容拷贝到”+号剪贴板，即系统剪贴板。

`“+p` 把系统剪贴板的内容粘贴到vim。

Vim 中的剪贴板历史可以使用 `:reg` 进行罗列，并配合以上操作进行粘贴。


### 2. 如何连接两行文字？

在 Vim 中你可以把两行连起来这意味着删除两行间的换行符。"J" 命令用于完成这个
功能。
以下面两行为例:

```
A young intelligent
turtle
```

把光标移到第一行，然后按 "J":

```
A young intelligent turtle
```

### 3. 如何进行列块选择？

使用 `Ctrl + v` 进入列块选择模式。

### 4. 如何快速查找和替换？

快速进行查找可以在命令模式下，使用 `/{regex}`，Vim 中的正则有点特殊（详细规则可以阅读文末附件，最明显的就是括号也需要转义），举个例子，有文字材料如下：

```
java.lang.NullPointerException
...
java.lang.ClassNotFoundException
```

要求查找到所有 Exception，那么可以使用

```
/\.\a*Exception
```

再配合 `n` 继续查找。

### 5. 如何进行重做？

使用 `Ctrl + r` 进行重做。

### 6. 如何记录和回放我的可重复性操作？

要点：使用 q{register} 和 @{register}

"." 命令重复前一个修改操作。但如果你需要作一些更复杂的操作它就不行了。这时，记
录命令就变得很有效。这需要三个步骤:

1. "q{register}" 命令启动一次击键记录，结果保存到 {register} 指定的寄存器中。
  寄存器名可以用 a 到 z 中任一个字母表示。

2. 输入你的命令。

3. 键入 q (后面不用跟任何字符) 命令结束记录。

现在，你可以用 "@{register}" 命令执行这个宏。
现在看看你可以怎么用这些命令。假设你有如下文件名列表:

```
stdio.h
fcntl.h
unistd.h
stdlib.h
```

而你想把它变成这样:
```
include "stdio.h"
include "fcntl.h"
include "unistd.h"
include "stdlib.h"
```

先移动到第一行，接着执行如下命令:

```
qa 启动记录，并使用寄存器 a
^ 移到行首
i#include " 在行首输入 #include "
$ 移到行末
a" 在行末加上双引号 (")
j 移到下一行
q 结束记录
```

现在，你已经完成一次复杂的修改了。你可以通过重复三次 "@a" 完成余下的修改。
"@a" 命令可以通过计数前缀修饰，使操作重复指定的次数。在本例中，你可以输入:

```
3@a
```

## 附录

[Vim 用户使用手册](https://drive.google.com/file/d/1XVwX2IKHfsmtuptZNhpo0DnjMUod65ST/view?usp=sharing)