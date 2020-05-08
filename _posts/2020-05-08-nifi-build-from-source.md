---
title: "从源码构建 nifi-standard-bundle"
layout: post
category: daily-note
tags: [nifi, build]
excerpt: "记录从源码构建nifi-standard-bundle时遇到的问题"
---

# 构建前准备

- jdk版本为1.8

# 构建步骤
 
```
// 在根目录下执行
mvn -T C2.0 clean install -Ddir-only

cd ./nifi-nar-bundles/nifi-standard-bundle

mvn clean package -DskipTests
```

# 常见问题

1. 编译groovy文件时，找不到类？

    答：检查mvn中的jdk版本是否为1.8。

1. 构建时报跟nifi-api有关的异常

    答：首先install nifi-api
