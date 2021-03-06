---
title: "Stack 源码分析"
layout: post
category: jdk
tags: [collection, jdk8]
excerpt: "Stack 数据结构源码分析"
---
Stack是一种后进先出（LIFO）的数据结构，它扩展了Vector类，增加了5个具有Stack特性的方法。由于 Vector 是线程安全的，Stack 的大多数方法继承了 Vector 的线程安全特性，对于 5 个特有的方法，也通过 synchronized 机制保证了线程安全。

## 方法介绍

```
public class Stack<E> extends Vector<E> {
    /**
     * 创建空栈
     */
    public Stack() {
    }

    /**
     * 将元素压入栈顶
     */
    public E push(E item) {
        addElement(item);

        return item;
    }

    /**
     * 弹出栈顶元素
     */
    public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }

    /**
     * 获取栈顶元素值，不弹出
     */
    public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }

    /**
     * 判断栈是否为空
     */
    public boolean empty() {
        return size() == 0;
    }

    /**
     * 栈中查找与o相等的元素，并返回距离栈顶的距离（从1开始）
     */
    public synchronized int search(Object o) {
        int i = lastIndexOf(o);

        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
}
```
其中，比较重要的方法是`push`、`pop`、`elementAt`和`search`。下面分别说明，方法内部是如何执行的。首先来看`push`方法。

### push

push方法比较简单，真正的逻辑在[Vector](https://dophinlife.github.io/jdk/Vector-source-code-analysis) 的addElement(E)方法中。

# pop

移除栈顶元素，并返回移除的元素。代码如下：
```
/**
 * 方法内部的实现是 get then remove
 * synchronized 保证操作的原子性，线程安全
 */
public synchronized E pop() {
    E       obj;
    // 获取元素的个数
    int     len = size();
    // 获取栈顶元素
    obj = peek();
    // 移除栈顶元素
    removeElementAt(len - 1)

    return obj;
}
```

## peek

获取栈顶元素的值，不删除。代码如下：
```
/**
 * 先获取长度，在取值
 * synchronized 复合操作的原子性
 */
public synchronized E peek() {
    int     len = size();

    if (len == 0)
        throw new EmptyStackException();
    return elementAt(len - 1);
}
```