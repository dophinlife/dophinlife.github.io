---
title: "Linux 系统参数及命令使用说明"
layout: post
category: daily-note
tags: [linux, system]
excerpt: "平时工作中，总会遇到一些linux的参数或者命令。工作工程中遇到的命令记录
在此处，下次再用到时查阅这里就行了，不用每次都花费时间去google."
---

# 系统参数

## open files 相关

### 查看 & 修改

通过 ulimit 可以查看当前的限制是多少:

```
$ ulimit -n
$ ulimit -Sn
$ ulimit -Hn
```
ulimit 中，每个限制都有两种类型：

- -S, soft limit, 软限制，用户可以上调软限制到硬限制
- -H, hard limit, 硬限制，非 root 用户不能修改

临时修改使用 ulimit 命令，以修改 open file(s) 为例。

```
$ ulimit -n 1024000
$ ulimit -n
```

永久修改需要修改 /etc/security/limits.conf 或者在 /etc/security/limits.d/ 目录下添加一个文件。
具体格式参考 /etc/security/limits.conf，里面有详细说明。

### 设置上限

open file(s) kernel 级别有 2 个配置，分别是：

```
fs.nr_open，进程级别
fs.file-max，系统级别
```
s.nr_open 默认设置的上限是 1048576，所以用户的 open file(s) 不可能超过这个上限。

```
# sysctl -w fs.nr_open=10000000
# ulimit -n 10000000
# ulimit -n
10000000
```

修改后即可设置更大的 open file(s) 了。

同样，对于 kernel 参数的修改，sysctl 命令修改的是当前运行时，如果需要永久修改， 则将配置添加到 
/etc/sysctl.conf 中，例如：

```
# echo "fs.nr_open = 10000000" >> /etc/sysctl.conf
# echo "fs.file-max = 11000000" >> /etc/sysctl.conf
```

注意：fs.nr_open 总是应该小于等于 fs.file-max。

如果要查看当前打开的文件数，使用下面的命令：

```
# sysctl fs.file-nr
fs.file-nr = 1760       0       11000000
```

不过，增大这些值意味着能够打开更多的文件（在 Linux 中，everything is file，包括 socket），但是同时也意味着
消耗更多的资源，所以基本上在物理机上才会遇到这种问题。

# 常用命令