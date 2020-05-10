---
title: "Vector 源码分析"
layout: post
category: jdk
tags: [collection, jdk8, vector]
excerpt: "Vector 数据结构源码分析"
---
Vector 是一个可伸缩容量的数组对象。

## fields 描述

```
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    /**
     * 存放 Vector 中的元素，容量不小于元素的总个数
     */
    protected Object[] elementData;

    /**
     * Vector 中的元素个数
     */
    protected int elementCount;

    /**
     * 需要扩容时，自动增加的容量
     */
    protected int capacityIncrement;
}

```
## 构造函数

```
/**
 * 为什么没有校验 capacityIncrement
 */
public Vector(int initialCapacity, int capacityIncrement) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
    // 构造函数中分配内存，并不是lazy init
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}

public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}

public Vector() {
    this(10);
}
```

## 方法介绍

### addElement

该方法将元素添加到vector的末尾，vector的size加1。如果size超过vector的容量，将进行扩容。

```
public synchronized void addElement(E obj) {
    /**
     * 结构化修改的次数，结构化修改指大小变化或其他可能导致不
     * 正确迭代遍历的情况，实现iterator的fail-fast机制中有用到
     */
    modCount++;
    // 将元素添加到vector内部的数组中
    add(obj, elementData, elementCount);
}
```
add方法的逻辑如下所示：
```
private void add(E e, Object[] elementData, int s) {
    // 判断vector内部的数组是否已经填满
    if (s == elementData.length)
        // 满了进行扩容
        elementData = grow();
    // 将元素添加到末尾
    elementData[s] = e;
    // size 加 1
    elementCount = s + 1;
}
```
扩容的逻辑如下：
```
private Object[] grow() {
    // 指定扩容的最小容量为原容量 + 1
    return grow(elementCount + 1);
}

// 扩容的最小容量为 minCapacity
private Object[] grow(int minCapacity) {
    return elementData = Arrays.copyOf(elementData,
                                       newCapacity(minCapacity));
    }

// 真正进行扩容的地方
private int newCapacity(int minCapacity) {
    int oldCapacity = elementData.length;
    /** 
     * capacityIncrement 扩容的步长，在构造函数中设置；
     * 如果没有设置，新的容量是原来容量的 2 倍
     */
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    // 如果扩容后的容量仍小于 minCapacity，直接将容量扩大为 minCapacity
    if (newCapacity - minCapacity <= 0) {
        if (minCapacity < 0) // 超容
            throw new OutOfMemoryError();
        return minCapacity;
    }

    /** 
      * MAX_ARRAY_SIZE 为 Integer.MAX_VALUE - 8
      * 如果大于扩容后 MAX_ARRAY_SIZE，将容量设置为 Integer.MAX_VALUE
      *
      * hugeCapacity(minCapacity)是将容量扩大为 Integer.MAX_VALUE，逻辑较简单，这里不列出源码
      */
    return (newCapacity - MAX_ARRAY_SIZE <= 0)
        ? newCapacity
        : hugeCapacity(minCapacity);
}
```