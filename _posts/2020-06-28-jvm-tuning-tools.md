---
title: "JVM 调试常用工具"
layout: post
category: daily-note
tags: [jvm tools]
excerpt: "jvm 问题诊断常用工具学习记录"
---
# jstat

监控 JVM 统计数据。

命令行形式如下:
```
jstat [ generalOption | outputOptions vmid [ interval[s|ms] [ count ] ]
```

## 参数说明

### generalOption

没啥说的，参阅 [文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html#BEHHGFAE) 。

### OutputOptions

#### -statOption
用于指定 jstat 命令输出的具体统计信息，详细的参数如下：
##### class

类加载相关的指标。输出如下所示：
![jstat-class](../assets/img/posts/202006/jstat-class.png)

- Loaded: 已加载的 class 数目
- Bytes: 已加载的 class 大小，单位kB
- Unloaded: 已卸载的 class 数目
- Bytes: 已卸载的 class 大小，单位kB
- Time: class 加载卸载花费的时间，单位s

##### compiler
JVM jit 编译相关的统计。输出格式如下所示:
![jstat-compiler](../assets/img/posts/202006/jstat-compiler.png)

- Compiled: 已执行的编译任务数
- Failed: 执行失败的编译任务数
- Invalid: 无效的编译任务数
- Time: 即时编译花费的时间，单位s
- FailedType: 上次编译失败的编译类型
- FailedMethod: 上次编译失败的类和方法

##### gc
堆 GC 相关的统计。输出格式如下：
![jstat-gc](../assets/img/posts/202006/jstat-gc.png)
- S0C: 当前 s0 容量，单位kB
- S1C: 当前 s1 容量，单位kB
- S0U: s0 使用量，单位kB
- S1U: s1 使用量，单位kB
- EC: 当前 eden 区容量，单位kB
- EU: Eden 区使用量，单位kB
- OC: 老年代容量，单位kB
- OU: 老年代使用量，单位kB
- MC: Metaspace 容量，单位kB
- MU: Metaspace 使用量，单位kB
- CCSC: Compressed class space 容量，单位kB
- CCSU: Compressed class space 使用量，单位kB
- YGC: 年轻代 GC 事件数
- YGCT: 年轻代 GC 花费总时间，单位 s
- FGC: full GC 事件数
- FGCT: full GC 花费总时间，单位 s
- GCT: GC 总时间，单位 s

##### gccapacity
内存分代容量。输出格式如下：
![jstat-gccapacity](../assets/img/posts/202006/jstat-gccapacity.png)
- NGCMN: 新生代最小容量，单位 kB
- NGCMX: 新生代最大容量，单位 kB
- NGC: 新生代当前容量，单位 kB
- S0C: s0 当前容量，单位 kB
- S1C: s1 当前容量，单位 kB
- EC: Eden 区当前容量，单位 kB
- OGCMN: 老年代最小容量，单位 kB
- OGCMX: 老年代最大容量，单位 kB
- OGC: 老年代当前容量，单位 kB
- OC: 老年代当前容量，单位 kB
- MCMN: Metaspace 最小容量，单位 kB
- MCMX: Metaspace 最大容量，单位 kB
- MC: Metaspace 当前容量，单位 kB
- CCSMN: Compressed class space 当前容量，单位 kB
- CCSMX: Compressed class space 当前容量，单位 kB
- CCSC: Compressed class space 当前容量，单位 kB
- YGC: 年轻代 GC 事件数
- FGC: Full GC 事件数

    OGC = sum(OC)
##### gcutil
GC 统计概要。输出格式如下所示：
![jstat-gcutil](../assets/img/posts/202006/jstat-gcutil.png)
- S0: s0 当前使用量与 s0 当前容量的百分比
- S1: s1 当前使用量与 s1 当前容量的百分比
- E: Eden 区当前使用量与 eden 区当前容量的百分比
- O: Old 区当前使用量与 old 区当前容量的百分比
- M: Metaspace 当前使用量与 metaspace 当前容量的百分比
- CCS: Compressed class space 当前使用量与 Compressed class space 当前容量的百分比
- YGC: 年轻代 GC 事件数
- YGCT: 年轻代 GC 花费时间，单位 s
- FGC: Full GC 事件数
- FGCT: Full GC 花费时间，单位 s
- GCT: GC 花费时间，单位 s
##### gccause
显示上次 GC 事件和当前 GC 事件的详细信息。输出格式如下：
![jstat-gccause](../assets/img/posts/202006/jstat-gccause.png)
- LGCC: 上次 GC 原因
- GCC: 本次 GC 原因
- 其他参数与 gcutil 输出一样


##### 其他参数请参考 [官方文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html#BEHHGFAE)
- gcnew
- gcnewcapacity
- gcold
- gcoldcapacity
- gcmetacapacity
- printcompilation

#### -h n
每 n 次采样显示一次列头部信息，默认值为0，表示仅在第一行显示。
#### -t
显示 timestamp 作为第一列，表示 JVM 启动到现在的秒数。