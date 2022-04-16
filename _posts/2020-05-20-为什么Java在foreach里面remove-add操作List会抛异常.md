---
title: 为什么Java在foreach里面remove/add操作List会抛异常
categories: java
date: 2020-05-20 21:06:52
img_path: /assets/
tags: java
---
## 背景

平常工作大家都只是知道删除List的元素要用迭代器(iterator)，用foreach判断删除的话会抛异常。但是为什么会抛异常呢？ <!-- more -->来，先看看阿里巴巴Java开发手册中是如何写道的：

![](为什么Java在foreach里面remove-add操作List会抛异常.assets/foreach-remove_add.png)

**删除”1“不会抛异常，但是”2“却会。很神奇是不是**？

```java
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:901)
	at java.util.ArrayList$Itr.next(ArrayList.java:851)
	at exercise.Main.main(Main.java:12)
```

## 源码分析

话不多说，直接上代码。先来看看会走哪些代码。**请注意看我加的中文注释。**

### 迭代器iterator(ArrayList内部类)

````java
private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    // 内部的iterator会自己保存一份List的修改次数expectedModCount
    int expectedModCount = modCount;

    public boolean hasNext() {
        // 每次迭代先走这里这 判断是否有下一个元素
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    // 如果hasNext 为true 则进入该方法
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        // 游标自增
        cursor = i + 1;
        // 返回元素 （其实就是进入了for循环体，进行咱们的元素值判断)
        return (E) elementData[lastRet = i];
    }
````

其实**Java的foreach循环的底层实现就是用的iterator迭代器**。ArrayList会使用内部自己的Iterator。先用hasNext()判断是否有下一个元素后，会调用next()来返回下一个元素。

#### checkForComodification()

````java
final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
````

其实内部的iterator会自己保存一份List的修改次数expectedModCount。初始的时候是等于modCount的。checkForComodification()逻辑很简单就是**判断了下expectedModCount、modCount两个值是否不等。**

### ArrayList方法

#### remove()方法

````java
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
````

只要传入的参数o不为空，就会走第二个for循环中的fastRemove(）。

#### fastRemove(）

````Java
/*
 * Private remove method that skips bounds checking and does not
 * return the value removed.
 */
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    // 注意这里，删除完后List的size就会减1
    elementData[--size] = null; // clear to let GC do its work
}
````

这里就是实际的删除操作了。这里会把修改计数器自增。并且List的size会减1

## 为什么删除”1“不会抛异常，但是”2“却会？

其实很简单。因为判断删除"2"的时候会多走一次循环。**因为hasNext()返回了false**。下面直接画个表格。

| 循环次数(循环后) | cursor |        size         |             modCount             | expectedModCount |
| :--------------: | :----: | :-----------------: | :------------------------------: | :--------------: |
|   0（初始值）    |   0    |          2          | **2(一共两个元素修改次数就是2)** |        2         |
|        1         |   1    |          2          |                2                 |        2         |
|        2         | **2**  | **1(删除后size--)** | **3（fastRemove中modCount++）**  |        2         |

**第二次循坏后，因为cursor!=size会导致进入第三次循环，进入next()将直接导致modCount!=expectedModCount，最后抛出ConcurrentModificationException。**

而判断删除“1”的时候不会报错是因为"1"处在第一个元素，进入第一次循环后cursor和size刚好是相等的，不会进入下次循环。表格如下

| 循环次数(循环后) | cursor | size  | modCount | expectedModCount |
| :--------------: | :----: | :---: | :------: | :--------------: |
|   0（初始值）    |   0    |   2   |    2     |        2         |
|        1         | **1**  | **1** |    3     |        2         |