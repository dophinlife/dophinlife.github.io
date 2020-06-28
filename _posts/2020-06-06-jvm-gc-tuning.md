---
title: "JVM GC 调优"
layout: post
category: daily-note
tags: [java, gc]
excerpt: "记录jvm调优的相关参数的学习过程"
---

# 环境

- jdk版本为 12.0.2
- OS 为 macOS Catalina 10.15.4
- cpu 2.2 GHz 6-Core Intel Core i7
- memory 16 GB 2400 MHz DDR4

# 测试代码

```java
public class Solution {
    private final static int size = 8 * 1024 * 1024;
    public static void main(String[] args) {
        while (true) {
            System.out.println("haha...");
            int[] arr = new int[size];
            Arrays.fill(arr, 0);
        }
    }
}
```

# GC、堆以及JIT的默认选项

如果一台机器满足以下条件会被判定为服务端机器:

1. 两个及以上的处理器；
1. 2G及以上的物理内存。

对于服务端的JVM，会采用如下的默认选项：
1. G1垃圾收集器；
1. 初始堆内存为物理内存的1/64；
1. 最大堆内存为物理内存的1/4；
1. 分层的编译器，同时启用从C1和C2。

不设置任何的jvm参数，堆的大小统计如下：
```
Current heap size: 146,406 kbytes
// 最大堆内存为4G
Maximum heap size: 4,194,304 kbytes

Committed memory: 1,156,096 kbytes

Pending finalization: 0 objects
// G1 垃圾收集器
Garbage collector: 
Name = 'G1 Young Generation', Collections = 690, Total time spent = 0.711 seconds
Garbage collector: 
Name = 'G1 Old Generation', Collections = 0, Total time spent = 0.000 seconds
```

# 调优目标

GC 的调优目标通常有两个：暂停时间(延迟时间、响应时间)和吞吐量，一般会选择其中的一个作为主要目标，
在达到的基础上，调整另外一个目标。这些目标并不总是可以满足的：应用数据需要最小的堆存放所有的运行
时数据，而其他的配置可能会阻止达到期望的目标。

## 暂停时间



## 吞吐量

