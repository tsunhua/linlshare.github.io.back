---
title: Python 基础
date: 2019-05-15 16:02:19
tags: [Python]
---

## Python 版本管理器：pyenv

### zsh 配置

```shell
# 安装
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
## 使用 MacOS时可以通过 Homebrew 进行安装
brew update
brew install pyenv
brew install zlib
brew install sqlite
# 添加环境变量到 .bashrc 并使之生效
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
$ exec "$SHELL"
# 更新
pyenv update
# 卸载
rm -fr ~/.pyenv
## 从 .bashrc 移除上面添加的环境变量
```

### 使用

```shell
# 安装 python
pyenv install 2.7.8
# 设置本地版本
pyenv local 3.4.10
# 设置全局版本
pyenv global 2.7.8
```

## Python 包管理器：pip

### 安装

> 在 Python2 >= 2.7.9 或者 Python3 >=3.4 时，默认已经安装了 pip 了。

```shell
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

### 使用

```shell
# 安装软件包
pip install SomePackage              # 最新版本
pip install SomePackage==1.0.4       # 指定版本
pip install 'SomePackage>=1.0.4'     # 最小版本
# 升级
pip install --upgrade SomePackage
# 卸载
pip uninstall SomePackage
# 搜索包
pip search SomePackage
# 显示指定包的详细信息
pip show -f SomePackage
# 列出已经安装的包
pip list
```

> 注意：
>
> 同时使用 Python2 和 Python3 时，使用下述方式：
>
> ```shell
> python2 -m pip install SomePackage
> python3 -m pip install SomePackage
> ```