# ArrayList
**基本特点**：

ArrayList相当于动态数组。

内存地址连续，查询快，增删慢。

线程不是安全的，建议在单线程中才使用ArrayList，而在多线程中可以选择Vector或者CopyOnWriteArrayList。

**初始化**：

首先ArrayList提供了3种初始化构造函数。

1.无参构造，初始化一个空的数组elementData，添加元素时候再扩容。

```java
List<String> list = new ArrayList<String>();
```
2.指定容量的构造，直接初始化为指定大小。

```java
List<String> list = new ArrayList<String>(20);
```
3.带有一个集合参数的构造，把指定集合中的数据通过Arrays.copyOf拷贝到elementData中，容量和指定集合容量相同。

```java
List<String> list = new ArrayList<String>(Arrays.asList("apple", "banana", "orange"));
```
**扩容**：

添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 oldCapacity + (oldCapacity >> 1)，也就是旧容量的 1.5 倍。

扩容操作需要调用 Arrays.copyOf() 把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。