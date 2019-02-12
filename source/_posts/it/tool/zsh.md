---
title: zsh
date: 2018-11-28 11:55:54
tags: [zsh]
---

### 禁止粘贴命令时自动对特殊字符进行转义

（1）编辑 `misc.zsh` 文件

```shell
vim  ~/.oh-my-zsh/lib/misc.zsh
```

（2）注释掉 `url-quote-magic`

```
#if [[ $ZSH_VERSION != 5.1.1 ]]; then
#  for d in $fpath; do
#       if [[ -e "$d/url-quote-magic" ]]; then
#               if is-at-least 5.1; then
#                       autoload -Uz bracketed-paste-magic
#                       zle -N bracketed-paste bracketed-paste-magic
#               fi
#               autoload -Uz url-quote-magic
#               zle -N self-insert url-quote-magic
#      break
#       fi
#  done
#fi
```

### 解决中文乱码

在`~/.zshrc`添加

```shell
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
```

然后执行

```shell
source ~/.zshrc
```

