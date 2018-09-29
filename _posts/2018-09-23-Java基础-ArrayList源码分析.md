---
layout: post
title:  "Java基础-ArrayList源码分析"
date:   2018-09-23 16:14:31 +0800
categories: Java基础
tags: ArrayList
---

* content
{:toc}

## ArrayList概述
首先声明一下接下来的几篇关于容器源码的分析都是基于JDK1.8的。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
```

从ArrayList的声明中我们可以看到，其实现了RandomAcess接口，但是LinkedList没有实现这一接口，先说一下RandomAccess接口的作用。

查看RandomAccess源码：
```java
public interface RandomAccess {
}
```
可以看到其实一个标记接口(Maker Interface),其作用是实现了这个接口就支持随机访问，由于ArrayList底层是数组，所以支持随机访问是理所当然的。

通过Collections中的shuffle()方法来加深理解：
```java
public static void shuffle(List<?> list, Random rnd) {
    int size = list.size();
    if (size < SHUFFLE_THRESHOLD || list instanceof RandomAccess) {
        for (int i=size; i>1; i--)
            swap(list, i-1, rnd.nextInt(i));
    } else {
        Object arr[] = list.toArray();

        // Shuffle array
        for (int i=size; i>1; i--)
            swap(arr, i-1, rnd.nextInt(i));

        // Dump array back into list
        // instead of using a raw type here, it's possible to capture
        // the wildcard but it will require a call to a supplementary
        // private method
        ListIterator it = list.listIterator();
        for (int i=0; i<arr.length; i++) {
            it.next();
            it.set(arr[i]);
        }
    }
}
```
从上述源码中可以看出如果一个类实现了RandomAccess接口，那么在该类的方法就可以使用`instanceof`来判断该类是否实现了RandomAccess接口，也就实现该接口`标记`的作用。

## 属性
接着看ArrayList的源码，首先是其属性。

```java
/** ArrayList默认的容量为10
*/
private static final int DEFAULT_CAPACITY = 10;

/**
 * 这是一个共享的空的数组实例，当使用new ArrayList(0) 或者new ArrayList(Collection<? extends E> c) 
 * 并且 c.size() = 0 的时候将elementData 数组讲指向这个实例对象。
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

/**
 * 另一个共享空数组实例，再第一次add元素的时候将使用它来判断数组大小是否设置为DEFAULT_CAPACITY
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * 真正装载集合元素的底层数组 
 * transient表示该成员变量无法被Serializable 序列化 
 */
transient Object[] elementData; // non-private to simplify nested class access

/**
 * ArrayList的长度(the number of elements it contains).
 */
private int size;
```

## 构造方法
ArrayList 一共三种构造方式，我们先从无参的构造方法来开始

### 无参构造
```java
/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```
用得最多的一个构造方法，源码注释显示这会构造一个初始容量为10的空列表，但是其指向的是`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`这个空数组。

原因在于调用`add`方法的时候会调用`ensureCapacityInternal`方法进行容量的判断：
```java
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
       minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}
```
所以在第一次使用add方法的时候容量就被设为10.

### 指定初始容量的构造方法
```java
/**
 * Constructs an empty list with the specified initial capacity.
 *
 * @param  initialCapacity  the initial capacity of the list
 * @throws IllegalArgumentException if the specified initial capacity
 *         is negative
 */
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
    }
}
```
这个构造方法的意义在于，如果在使用ArrayList时知道其里面容纳的element的个数，那么推荐使用该方法，因为如果让ArrayList自动扩容会产生一定的内存开销，但这个开销在知道容量的时候时可以避免的。

从源码中可以看出当给定容量为0时，会使用`EMPTY_ELEMENTDATA`作为暂存数组，这是这个空数组的一个用处，当容量大于0时会给elementData创建一个数组。

### 使用另个一个集合 Collection 的构造方法
```java
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```
该构造方法主要涉及两个方法，一个是Collection.toArray(),还有一个是Arrays.copyof()。

#### Collection.toArray()
首先来看Collection.toArray()，这是Collection接口定义的方法，具体实现在其实现类中.
观察下面一段代码
```java
public static void main(String[] args) {
	ArrayList<String> list = new ArrayList<>();
	list.add("hello");
	list.add("world");
	list.add("java");
		
	List<String> subclass = Arrays.asList("hello", "world", "java");
		
	Object[] objects = subclass.toArray();
	Object[] listArr = list.toArray();
		
	// class java.util.ArrayList
	System.out.println(list.getClass());
	// class java.util.Arrays$ArrayList
	System.out.println(subclass.getClass());
		
	// String[]
	System.out.println(objects.getClass().getSimpleName());
		
	// Object[]
	System.out.println(listArr.getClass().getSimpleName());
		
	// 正确执行
	listArr[0] = new Object();
	//java.lang.ArrayStoreException
	objects[0] = new Object();
}
```
通过观察可以知道通过Arrays.asList()方法得到的subclass其实是Arrays的内部类`class java.util.Arrays$ArrayList`,这个时候通过subclass.toArray()得到的数组是`String[]`,即`elementData.getClass() != Object[].class`，在测试代码中`objects[0] = new Object();`会报错`java.lang.ArrayStoreException`，所以在构造方法中要做这个判断。

具体可以接着看Arrays的源码。
```java
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}

private static class ArrayList<E> extends AbstractList<E>
    implements RandomAccess, java.io.Serializable
{   
    // ...
    
    @Override
    public Object[] toArray() {
        return a.clone();
    }
    
    // ...
}
```
通过上述源码可以，asList()返回的subclass是Arrays的内部类`Arrays$ArrayList`，所以将subclass作为参数放入构造方法中调用的`toArray()`是Arrays内部类的方法，即`a.clone()`，这个clone()方法不会改变数组的类型，所以当原始数组放的是String类型，返回的还是String类型，此时对这个数组操作就会出现`java.lang.ArrayStoreException`的错误。

其实我们可以认为这是 jdk 的一个 bug，早在 05年的时候被人提出来了，但是一直没修复，但是在新的 「jdk 1.9」 种这个 bug 被修复了。

可以参考[bug 6260652][1]获取更多信息。


#### Arrays.copyOf 方法
这个方法是在集合源码中常见的一个方法，他有很多重载方式,我们来看下最根本的方法：
```java
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        // 通过反射构造泛型数组
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    // 使用native方法批量赋值元素到copy中
    System.arraycopy(original, 0, copy, 0, Math.min(original.length, newLength));
    return copy;
}
```
可以看出在`Arrays.copyof()`方法中首先判断指定数组类型是否为Object[],如果不是就使用反射区构造是定类型的数组，然后用System.arraycopy()实现数组的复制并返回。
其中`newlength`如果比`originLength`大，那么其余部分就为null，如：
```java
String[] strArr = {"love", "minmin"};
Object[] objArr = Arrays.copyOf(strArr, 5, Object[].class);
// [love, minmin, null, null, null]
System.out.println(Arrays.toString(objArr));
```
不过ArrayList源码中，传递的newlength为`c.siez()`,所以不会出现null。

补充资料：[System：System.arraycopy方法详解][2]

## 扩容
这块内容是面试的重点，先看一下如何添加元素。
```java
private int size;

public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```
以上是扩容操作，首先会调用`ensureCapacityInternal`方法了检查是否需要扩容，接下来就是常规的数组添加元素操作。

注意，size是成员变量，在创建集合是初始默认为0.

```java
// AbstractList类中的成员变量
protected transient int modCount = 0;

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    // 扩容判断
    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    // 操作数+1，用于保证并发访问
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
```
以上代码进行了扩容前的判断工作，当使用无参构造创建ArrayLsit，第一次进行add时，执行`if (minCapacity - elementData.length > 0)`结果为10-0 > 0,所以会进行`grow()`方法。

```java
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}
```
从源码中可以看到`newCapacity`设为原来`elementData.length`的`1.5`倍，`minCapacity`为`size+1`或者`DEFAULT_CAPACITY`
主要设计的知识点为：
1.每次扩容都扩大为原来size的1.5倍（如果newCapacity> MAX_ARRAY_SIZE，return (minCapacity > MAX_ARRAY_SIZE) ?Integer.MAX_VALUE : MAX_ARRAY_SIZE)
2.每次扩容都要将原来的数组拷贝到新的的数组，所以扩容是比较消耗性能的。

### 在指定坐标添加元素
```java
public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1,size - index);
    elementData[index] = element;
    size++;
}

private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```
主要过程是把index往后的元素拷贝到index+1往后的位置，空出index给新加的元素。

### 批量添加元素
```java
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}
```
主要步骤为使用`System.arraycopy`方法将Object[]数组中的内容拷贝到原来数组后面。

### 在指定位置批量添加
```java
public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);

    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount

    int numMoved = size - index;
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew, numMoved);

    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}
```
与批量添加元素主要的不同是对于numMoved做了判读，如果numMoved>0,说明index在0-size之间，需要移动部分元素，如果numMoved<=0，说明index在size之后，不需要移动元素，只需要把添加的元素copy到指定index。

## 删除元素
```java
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}

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

private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
```
注意：
1.方法remove(int index)中会首先进行`rangeCheck`，因为`size`不一定等于`element.length`,所以要做index >= size的判断。
2.需要调用 System.arraycopy() 将 index+1 后面的元素都复制到index位置上，该操作的时间复杂度为 O(N)，可以看出ArrayList删除元素的代价是非常高的。
3.如果数组中有指定多个与o相同的元素只会移除角标最小的那个，并且 null 和 非null 的时候判断方法不一样。

### removeAll与retainAll
```java
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, false);
}

public boolean retainAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, true);
}

private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        for (; r < size; r++)
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // Preserve behavioral compatibility with AbstractCollection,
        // even if c.contains() throws.
        if (r != size) {
        System.arraycopy(elementData, r,
                         elementData, w,
                         size - r);
            w += size - r;
        }
        if (w != size) {
            // clear to let GC do its work
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}
```

## contains()
```java
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}

public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))                   return i;
    }
    return -1;
}
```
contains()方法要注意的是java中equals方法默认比较的是两个对象的引用，所以当ArrayList中存放的是对象时，要改写对象的equals()方法
```java
List<Student> stulist = new ArrayList<>();
	Student s1 = new Student(18, "minmin");
	Student s2 = new Student(18, "minmin");
		
	stulist.add(s1);
		
	System.out.println(stulist.contains(s1)); // true
	System.out.println(stulist.contains(s2)); // false
```
从上面代码可以看出，因为Student类没有重写equals方法，所以第二个输出为false，如果业务需要按照内容判断contains则需要重写equals方法。

## Fail-Fast
modCount用来记录ArrayList结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。

在进行序列化或者迭代等操作时，需要比较操作前后`modCount`是否改变，如果改变了需要抛出 `ConcurrentModificationException`。

看一下代码：
```java
public static void main(String[] args) {
	List<Integer> list = new ArrayList<>();
	list.add(1);
	list.add(2);
	list.add(3);
	list.add(3);
	for(int i = 0; i<list.size(); i++) {
		if(list.get(i) == 3) {
			list.remove(i);
		}
	}
	System.out.println(list);
}

// 输出：[1, 2, 3]
```
首先上述代码没有报错，但是结果却不是想要的，原因在于remove()操作改变了size，本来size为4，但第一次remove之后，size变为3，循环直接结束，所以最后一个3没有remove。

```java
int n = list.size();
for(int i = 0; i<n; i++) {
	if(list.get(i) == 3) {
		list.remove(i);
	}
}
```
当把for循环改成上述代码，则会报错`java.lang.IndexOutOfBoundsException`，因为当i=3时，get(3)越界。

继续看下一段代码：
```java
public static void main(String[] args) {
	ArrayList<Integer> list = new ArrayList<Integer>();
    list.add(2);
    Iterator<Integer> iterator = list.iterator();
    while(iterator.hasNext()){
        Integer integer = iterator.next();
        if(integer==2)
            list.remove(integer);
    }
}

public boolean hasNext() {
    return cursor != size;
}

public E next() {
    checkForComodification();
    int i = cursor;
    if (i >= size)
        throw new NoSuchElementException();
    Object[] elementData = ArrayList.this.elementData;
    if (i >= elementData.length)
        throw new ConcurrentModificationException();
    cursor = i + 1;
    return (E) elementData[lastRet = i];
}

public void remove() {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();
        try {
        ArrayList.this.remove(lastRet);
        cursor = lastRet;
        lastRet = -1;
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}

final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```
上述代码会抛出`java.util.ConcurrentModificationException`，因为第一次remove操作之后,modCount增加了1，但是expectedModCount没变，所以第二次调用next()方法时首先执行checkForComodification()方法，随后抛出异常。

解决方法：
将list.remove()改为iterator.remove(),因为其多了以下一步操作,或者使用for加get遍历。
```java
expectedModCount = modCount;
```
多线程下的解决方法可参考这篇博客：[Java ConcurrentModificationException][3]

## 序列化(TODO)

总结：
1.允许存放（不止一个）null元素
2.允许存放重复数据，存储顺序按照元素的添加顺序
3.ArrayList 并不是一个线程安全的集合。如果集合的增删操作需要保证线程的安全性，可以考虑使用 CopyOnWriteArrayList或者使collections.synchronizedList(List)函数返回一个线程安全的ArrayList类.


  [1]: https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6260652
  [2]: https://segmentfault.com/a/1190000009922279
  [3]: http://www.cnblogs.com/dolphin0520/p/3933551.html