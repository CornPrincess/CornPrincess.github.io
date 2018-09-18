---
layout: post
title:  "Java基础-String"
date:   2018-09-18 23:59:31 +0800
categories: Java基础
tags: String
---

* content
{:toc}

### String
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```
从String的源码中可以看出，String为final类，不可以被继承，内部使用char[]进行储存数据，并且被声明为final，说明value数组初始化后就不能再引用其他数组，且String内部没有改变value数组的方法，因此可以保证String不变。

其中String不可变的好处见这篇[博客][1]

### String Pool
字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 `intern()` 方法在运行过程中将字符串添加到String Pool中。

当一个字符串调用 intern() 方法时，如果String Pool 中已经存在一个字符串和该字符串值相等（使用 equals()方法进行确定），那么就会返回String Pool中字符串的引用；否则，就会在String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

下面示例中s1和s2采用newString()的方式新建了两个不同字符串，而s3和s4是通过s1.intern()方法取得一个字符串引用。intern()首先把s1引用的字符串放到 String Pool中，然后返回这个字符串引用。因此 s3 和 s4 引用的是同一个字符串。
```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2); //false
	
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4); // true
```

如果采用直接赋值，那么会自动存放到常量池中
```java
String s5 = "bbb";
String s6 = "bbb";
System.out.println(s5 == s6); // true
```
在 Java 7 之前，String Pool 被放在运行时常量池中，它属于永久代。而在 Java 7，String Pool 被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 OutOfMemoryError 错误。

String s1 = new String("abc")与String s2 = "abc"的区别：
使用直接赋值只会在字符串常量池创建一个"abc"字符串对象，常量池中的数据是在编译期赋值的，也就是生成class文件时就把它放到常量池里了。

使用new String("abc"),不仅会在字符串常量池创建一个"abc"字符串对象，还会在堆中生成对象，堆区中是运行期分配的，这种方式比较浪费内存。
![new String("abc")][2]
如果是运行时对字符串相加或相减会放到堆中（放之前会先验证方法区中是否含有相同的字符串常量，如果存在，把地址返回，如果不存在，先将字符串常量放到池中，然后再返回该对象的地址）

### StringBuffer and StringBuilder
因为String 是不可变对象，如果多个字符串进行拼接，将会形成多个对象，这样可能会造成
内存溢出。
StringBuffer称为字符串缓冲区，它的工作原理是：预先申请一块内存，存放字符序列，如果字符序列满了，会重新改变缓存区的大小，以容纳更多的字符序列。StringBuffer是可变对象，这个是String最大的不同。
StringBuffer是线程安全的，可以在多线程环境下使用，内部使用synchronized进行同步。
StringBuffer是线程不安全的，在多线程环境下使用可能会有问题。

```java
StringBuffer sBuffer = new StringBuffer();
		
System.out.println(sBuffer.capacity()); // 16
System.out.println(sBuffer.length()); // 0
		
sBuffer.append("there is 17 chars");
system.out.println(sBuffer.capacity()); // 34
System.out.println(sBuffer.length()); //17
```

```java
/**
 * Constructs a string buffer with no characters in it and an
 * initial capacity of 16 characters.
 */
public StringBuffer() {
    super(16);
}

public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value, count);
    count += len;
    return this;
}

private void ensureCapacityInternal(int minimumCapacity) {
    // overflow-conscious code
    if (minimumCapacity - value.length > 0) {
        value = Arrays.copyOf(value,
            newCapacity(minimumCapacity));
    }
}
    
private int newCapacity(int minCapacity) {
    // overflow-conscious code
    int newCapacity = (value.length << 1) + 2;
    if (newCapacity - minCapacity < 0) {
        newCapacity = minCapacity;
    }
    return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
        ? hugeCapacity(minCapacity)
        : newCapacity;
}
```

StringBuffer和StringBuilder初始容量(capacity)为16.
进行字符串append添加的时候，会先计算添加后字符串大小，传入一个方法：ensureCapacityInternal 这个方法进行是否扩容的判断，需要扩容就调用newCapacity方法进行扩容：当容量不够时变为length*2+2.

  [1]: https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md#%E4%BA%8Cstring
  [2]: http://static.zybuluo.com/xiaocorn/0cp9hfcp64mxadcccphgwk5v/image_1cnmps4522s918t7has8ke1g0o9.png