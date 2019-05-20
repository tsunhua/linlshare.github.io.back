## CentOS yum 源修改

### 修改 CentOS 默认 yum 源为 mirrors.163.com

1. 首先备份系统自带yum源配置文件/etc/yum.repos.d/CentOS-Base.repo

```shell
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

2. 进入yum源配置文件所在的文件夹

```shell
[root@localhost ~]# cd /etc/yum.repos.d/
```

3. 下载163的yum源配置文件到上面那个文件夹内

```shell
`[root@localhost yum.repos.d]``# wget http://mirrors.163.com/.help/CentOS6-Base-163.repo`
```

4. 运行yum makecache生成缓存

```shell
[root@localhost yum.repos.d]# yum makecache
```

5. 这时候再更新系统就会看到以下mirrors.163.com信息

```shell
[root@localhost yum.repos.d]# yum -y update
已加载插件：fastestmirror, refresh-packagekit, security
设置更新进程Loading mirror speeds from cached hostfile

* base: mirrors.163.com
* extras: mirrors.163.com
* updates: mirrors.163.com
```

### 修改 CentOS 默认 yum 源为 mirrors.aliyun.com

1. 首先备份系统自带yum源配置文件/etc/yum.repos.d/CentOS-Base.repo

```shell
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

2. 下载ailiyun的yum源配置文件到/etc/yum.repos.d/

```shell
# CentOS7
[root@localhost ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# CentOS6
[root@localhost ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
```

3. 运行yum makecache生成缓存

```shell
[root@localhost ~]# yum makecache
```

4. 这时候再更新系统就会看到以下mirrors.aliyun.com信息

```shel
[root@localhost ~]# yum -y update
已加载插件：fastestmirror, refresh-packagekit, security
设置更新进程Loading mirror speeds from cached hostfile
* base: mirrors.aliyun.com
* extras: mirrors.aliyun.com
* updates: mirrors.aliyun.com
```