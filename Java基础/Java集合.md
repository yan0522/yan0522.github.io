# 前言

集合的最顶层接口为Iterable。

Iterable中封装了Iterator接口，只要实现了Iterable接口的类，就可以使用Iterator迭代器了。

这里特别说明一下：反过来能使用迭代器的未必就一定实现了该接口，例如数组的foreach不依赖迭代器。

集合框架图如下：

![集合框架体系图](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E4%BD%93%E7%B3%BB%E5%9B%BE.png)

## Iterator

什么是Iterator?

Iterator是顺序遍历迭代器，jdk中默认对集合框架中数据结构做了实现。

Iterator在实际应用中有一个比较好的点就是，可以一边遍历一遍删除元素。

实现此接口的实例可以对元素集合进行迭代遍历。

```java
public interface Iterator<E> {
    /**
     * 如果被迭代遍历的集合还没有被遍历完，返回True
     */
    boolean hasNext();

    /**
     * 返回集合里面的下一个元素
     */
    E next();

    /**
     * 删除集合里面上一次next()方法返回的元素
     *
     */
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    /**
     * 对剩余的每个元素执行给定的操作，直到所有元素*已处理完毕或该操作引发异常。
     * 如果指定了操作，则按迭代顺序执行操作。操作引发的异常会中继给调用者。
     * @since 1.8
     */
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```



## Collection及其子接口

### Collection

什么是Collection？

Collection接口是集合层次的根接口，在JDK中没有直接提供Collection接口的具体实现类，Collection的功能实现类主要是对它的三个更具体的子接口List、Set和Queue的具体实现类。

Collection接口定义了集合的一套通用操作的实现方法和命名规则，所有集合都是在Collection基础上进行扩展的。

List、Set、Queue接口都继承自Collection并定义了各自不同的方法对其扩展。

源码：

```java
/**
 * 集合层次结构中的根接口。一个集合代表一组对象，被称为元素。
 * 一些集合允许重复的元素，而有些则不允许。一些是有序的，另一些是无序的。
 * JDK没有提供这个接口的任何直接的实现：它提供更具体的子接口像 Set和 List实现。
 * 
 * 所有通用的Collection实现类（通常通过其子接口间接实现Collection）应该提供两个“标准”构造函数：
 * void（无参数） 构造函数，它创建一个空集合，以及一个带有Collection类型的单个参数的构造函数，
 * 它创建一个与它的参数具有相同元素的新集合。实际上，后者的构造函数允许用户复制任何集合，生成所需实现类型的等效集合。
 * 没有办法强制执行这个约定（因为接口不能包含构造函数），但是Java平台库中的所有通用Collection实现都符合。
 * 
 * 如果这个集合不支持某个操作的话，调用这些方法可能（但不是必需）抛出 UnsupportedOperationException 
 */

public interface Collection<E> extends Iterable<E> {

	/**
    * 此方法成功完成后，可确保对象包含在集合中。
    * 如果集合被修改，则返回true，如果没有更改，则返回false。
    */
    boolean add(E e)

	/**
    * 将指定集合中的所有元素添加到这个集合
    */
    boolean addAll(Collection<? extends E> c)

	/**
    * 从这个集合中移除指定元素的一个实例
    */
    boolean remove(Object o)

	/**
    * 删除此集合中包含的所有元素的所有元素。
    */
    boolean removeAll(Collection<?> c)
    
	/**
    * 删除满足给定谓词的这个集合的所有元素。
    */
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }

	/**
    * 从这个集合中移除所有不包含在指定集合中的元素的所有元素。
    */
    boolean retainAll(Collection<?> c)
    
    /**
    * 从这个集合中移除所有的元素
    */
    void clear()

	/**
	* 返回此集合中的元素的数目。如果这个集合包含多 Integer.MAX_VALUE元素，返回 Integer.MAX_VALUE。
	*/
    int size();
    
    /**
    * 如果此Collection不包含元素，则返回true
    */
    boolean isEmpty()
   
    /**
    * 如果集合包含指定元素，返回 true。
    */
    boolean contains(Object o)

	/**
    * 返回 true如果这个集合包含指定集合的所有元素。
    */
    boolean containsAll(Collection<?> c)
    
    /**
    * 返回一个包含此集合中包含的所有元素的新数组。
    * 如果实现已经排序了元素，它将以与迭代器返回的顺序相同的顺序返回元素数组。
    * 返回的数组不反映集合的任何更改。 即使底层数据结构已经是一个数组，也创建一个新数组。
    */
    Object[] toArray()
    
    /**
    * 返回包含此集合中包含的所有元素的数组。 如果指定的数组足够大以容纳元素，则使用指定的数组
    * 否则将创建相同类型的数组。 如果使用指定的数组并且大于此集合，则Collection元素之后的数组元素将设置为null。
    */
    <T> T[] toArray(T[] a)
   
    /**
    * 重写Object中的equals方法
    */
    boolean equals(Object o)
    
    /**
    * 重写Object中的hashCode方法
    */
    int hashCode()
    
    /**
    * 返回可用于访问此Collection所包含的对象的Iterator实例。 没有定义迭代器返回元素的顺序。
    * 只有当Collection的实例具有定义的顺序时，才能按照该顺序返回元素。
    */
    Iterator<E> iterator()
    
    /**
    * 返回一个并行迭代器类Spliterator
    */
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }
    
    /**
    * 这个集合中的元素的顺序 Stream
    */
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    
    /**
    * 返回一个可能并行 Stream与集合的来源。
    */
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
```

### List

List接口扩展了Collection，可以根据下标index对元素进行操作，每个元素都有唯一一个下标对应。

添加了功能更强大的ListIterator迭代器，可以沿任一方向遍历List，并且在遍历期间还可以修改List。

```java
/** 
 * List是维护其元素的排序的集合。 List中的每个元素都有一个索引。 因此，每个元素可以被其索引访问，第一个索引为零。 
 * 通常，与集合相比，List允许重复元素，其中元素必须是唯一的。
 * 有序集合（也称为<i>序列</ i>）。 该接口的用户可以精确控制每个元素插入到列表中的哪个位置。 
 * 用户可以通过整数索引（列表中的位置）访问元素，并搜索列表中的元素。
 * 
 * 列表允许重复元素，也允许null元素插入
 */  
public interface List<E> extends Collection<E> {  
    /**
     * List作为Collection的子接口提供了Collection接口定义的方法 
     * 这些方法在Collection源码中已经分析过了，就不在说明了 
     */  
    //增
    boolean add(E e); 
    boolean addAll(Collection<? extends E> c); 
    //删
    boolean remove(Object o); 
    boolean removeAll(Collection<?> c);
    boolean retainAll(Collection<?> c);
    default boolean removeIf(Predicate<? super E> filter)  
    void clear(); 
    //查
    int size();  
    boolean isEmpty();
    boolean contains(Object o);
    boolean containsAll(Collection<?> c);
    //转数组
    Object[] toArray();  
    <T> T[] toArray(T[] a);  
    //重写Object方法
    boolean equals(Object o);  
    int hashCode();
    //迭代器 
    Iterator<E> iterator();  
    default Spliterator<E> spliterator()
    
    //同时List接口定义了一些自己的方法来实现“有序”这一功能特点 
    /** 
     *返回列表中指定索引的元素
     */  
    E get(int index);  
    /** 
     *设定某个列表索引的值  
     */  
    E set(int index, E element);  
    /** 
     *在指定位置插入元素，当前元素和后续元素后移 
     *这是可选操作，类似于顺序表的插入操作 
     */  
    void add(int index, E element); 
    /**
    * 在指定位置插入指定集合中的元素，当前元素和后续元素后移
    */ 
    boolean addAll(int index, Collection<? extends E> c);  
    /** 
     * 删除指定位置的元素(可选操作)
     */  
    E remove(int index);  
    /** 
     * 获得指定对象的最小索引位置，没有则返回-1
     */  
    int indexOf(Object o);  
    /**
     * 获得指定对象的最大索引位置，可以知道的是若集合中无给定元素的重复元素下 
     * 其和indexOf返回值是一样的
     */  
    int lastIndexOf(Object o);  
    /**
     * 一种更加强大的迭代器，支持向前遍历，向后遍历 
     * 插入删除操作，此处不解释
     */  
    ListIterator<E> listIterator();  
    ListIterator<E> listIterator(int index);  
    /** 
     * 返回索引fromIndex(包括)和toIndex(不包括)之间的视图。
     */  
    List<E> subList(int fromIndex, int toIndex);  
}  
```

**List集合特点**

- 内部元素是有序的 ，集合中每个元素都有其对应的顺序索引。
- 元素是可以重复的 ，因为它可以通过索引来访问指定位置的集合元素。
- List接口有3个常用的实现类，分别是ArrayList、LinkedList、Vector。

### Set

Set接口完全继承了Collection，在Collection基础上并没有什么改动，但是增加了一个约定：不包含重复元素的集合！

**Set内部元素是无序的，元素是不可以重复的**

```java
/**
 * Set是不允许重复元素的数据结构。
 */
public interface Set<E> extends Collection<E> {
    /**
     * Set作为Collection的子接口提供了Collection接口定义的方法 
     * 这些方法在Collection源码学习中已经分析过了，就不在说明了 
     */  
   //增
    boolean add(E e); 
    boolean addAll(Collection<? extends E> c); 
    //删
    boolean remove(Object o); 
    boolean removeAll(Collection<?> c);
    boolean retainAll(Collection<?> c);
    default boolean removeIf(Predicate<? super E> filter)  
    void clear(); 
    //查
    int size();  
    boolean isEmpty();
    boolean contains(Object o);
    boolean containsAll(Collection<?> c);
    //转数组
    Object[] toArray();  
    <T> T[] toArray(T[] a);  
    //重写Object方法
    boolean equals(Object o);  
    int hashCode();
    //迭代器 
    Iterator<E> iterator();  
    default Spliterator<E> spliterator()
}
```

### Queue

**Queue接口特点**

- 先进先出的数据结构，即从容器的一端放入对象，从另一端取出，并且对象放入容器的顺序与取出的顺序是相同的。
- 虽然Queue接口继承Collection接口，但是Queue接口中的方法都是独立的，在创建具有Queue功能的实现时，不需要使用Collection方法。

```java
/**
 * Queue是在处理之前保存元素的集合。除了基本的Collection操作外，Queue还提供额外的插入、删除和检查操作
 * 每个Queue方法都有两种形式：（1）如果操作失败则抛出异常，
 * （2）如果操作失败，则返回特殊值（null或false，具体取决于操作），接口的常规结构如下表所示。
 * 插入操作的后一种形式是专为使用有容量限制 Queue实现；在大多数实现中，插入操作不会失败。
 * 
 * |  	      | 抛出异常         | 返回特殊值             |
 * |插入      | add(e)          | offer(e)              |
 * |删除      | remove()        | poll()                |
 * |检查      | element()       | peek()                |
 * 
 * 队列通常（但不一定）以FIFO（先进先出）方式对元素进行排序，优先级队列除外，
 * 它们根据元素的值对元素进行排序 — 有关详细信息，请参阅“对象排序”部分。
 * 无论使用什么排序，队列的头部都是通过调用remove或poll移除的元素。
 * 在FIFO队列中，所有新元素都插入队列的尾部，其他类型的队列可能使用不同的放置规则，
 * 每个Queue实现都必须指定其排序属性。
 *
 * Queue实现可以限制它所拥有的元素数量，这样的队列被称为有界，java.util.concurrent中的某些Queue实现是有界的，但java.util中的实现不是。
 * 可不只有抛出unchecked异常添加元素。的offer方法设计时使用的失败是正常的，
 * 而不是特殊的情况，例如，固定容量（或“有界”）队列。的remove()和poll()方法移除并返回队列的头部。
 * 从队列中删除的是队列的排序策略的函数，它不同于从“实现”到“实现”的功能。的remove()和poll()方法
 * 只是他们的行为当队列为空的不同的remove()方法抛出一个异常，而poll()方法返回null。
 *
 * Queue从Collection继承的add方法插入一个元素，除非它违反了队列的容量限制，
 * 在这种情况下它会抛出IllegalStateException。offer方法，仅用于有界队列，
 * 与add不同之处仅在于它通过返回false来表示插入元素失败。
 *
 * remove和poll方法都移除并返回队列的头部，确切地移除哪个元素是队列的排序策略的函数，
 * 仅当队列为空时，remove和poll方法的行为才有所不同，在这些情况下，
 * remove抛出NoSuchElementException，而poll返回null。
 *
 *element和peek方法返回但不移除队列的头部，它们之间的差异与remove和poll的方式完全相同：如果队列为空，则element抛出NoSuchElementException，而peek返回null。
 * 队列实现通常不允许插入null元素，为实现Queue而进行了改进的LinkedList实现是一个例外，
 * 由于历史原因，它允许null元素，但是你应该避免利用它，因为null被poll和peek方法用作特殊的返回值。
 *队列实现通常不定义equals和hashCode方法的基于元素的版本，而是从Object继承基于标识的版本。
 * Queue接口不定义阻塞队列方法，这在并发编程中很常见，这些等待元素出现或空间可用的方法在java.util.concurrent.BlockingQueue接口中定义，该接口扩展了Queue。
 *
 */
public interface Queue<E> extends Collection<E> {
    /**
     * 将指定的元素插入到此队列中，如果不违反容量限制立即执行此操作 
     * 成功后返回true，如果当前没有可用空间，则抛出IllegalStateException。
     */
    boolean add(E e);

    /**
     * 如果在不违反容量限制的情况下立即执行，则将指定的元素插入到此队列中。 
     * 当使用容量限制队列时，此方法通常优于add(E) ，这可能无法仅通过抛出异常来插入元素。 
     * 成功返回true，失败返回false
     */
    boolean offer(E e);

    /**
     * 检索并删除此队列的头。 此方法与poll不同之处在于，如果此队列为空，它将抛出异常。
     */
    E remove();

    /**
     * 检索并删除此队列的头，如果此队列为空，则返回 null 。
     */
    E poll();

    /**
     * 检索，但不删除，这个队列的头。 此方法与peek的不同之处在于，如果此队列为空，它将抛出异常。
     */
    E element();

    /**
     * 检索但不删除此队列的头部，如果此队列为空，则返回 null 。
     */
    E peek();
}
```



## ArrayList

ArrayList内部是使用动态数组实现的，换句话说，ArrayList封装了对内部数组的操作，比如向数组中添加、删除、插入新的元素或者数据的扩展和重定向。

- 继承了AbstractList,此类提供 List 接口的骨干实现，以最大限度地减少实现”随机访问”数据存储（如数组）支持的该接口所需的工作.对于连续的访问数据（如链表），应优先使用 AbstractSequentialList，而不是此类。
- 实现了List接口,意味着ArrayList元素是有序的,可以重复的,可以有null元素的集合。
- 实现了RandomAccess接口标识着其支持随机快速访问,实际上,我们查看RandomAccess源码可以看到,其实里面什么都没有定义.因为ArrayList底层是数组,那么随机快速访问是理所当然的,访问速度O(1)。
- 实现了Cloneable接口,标识着可以它可以被复制.注意,ArrayList里面的clone()复制其实是浅复制。
- 实现了Serializable 标识着集合可被序列化。

### ArrayList扩容

**初始化：**
ArrayList提供了三个构造函数来对elementData数组初始化：

1. 无参构造函数：初始化一个空的数组，添加元素时再对数组elementData扩容。
2. 指定容量的构造函数：直接初始化数组为指定的大小。
3. 带有一个集合参数的构造函数：把指定集合中的数据通过Arrays.copyOf拷贝到elementData中，容量和指定集合容量相同。

```java
private static final int DEFAULT_CAPACITY = 10;
private static final Object[] EMPTY_ELEMENTDATA = {};
transient Object[] elementData;
//无参构造函数直接赋值一个空的数组
 public ArrayList() {
        super();
        this.elementData = EMPTY_ELEMENTDATA;
    }
//指定大小的构造函数
public ArrayList(int initialCapacity) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
    }
//构造一个包含指定*集合的元素的列表。
public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        size = elementData.length;
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    }
```

**扩容：**
添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 oldCapacity + (oldCapacity >> 1)，也就是旧容量的 1.5 倍。

扩容操作需要调用 Arrays.copyOf() 把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。

```java
public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
private void ensureCapacityInternal(int minCapacity) {
        if (elementData == EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
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

### ArrayList迭代器实现

ArrayList通过内部类实现Iterator接口来实例化迭代器类，通过Iterator我们可以实现对elementData中的元素迭代遍历。而ArrayList又实现了一种功能更为强大的ListIterator来实现迭代遍历。ListIterator继承于Iterator接口，对Iterator接口做了扩展，支持向前向后遍历、迭代过程中去修改集合等。

```java
private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
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

        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            // update once at end of iteration to reduce heap write traffic
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }

private class ListItr extends Itr implements ListIterator<E> {
        ListItr(int index) {
            super();
            cursor = index;
        }

        public boolean hasPrevious() {
            return cursor != 0;
        }

        public int nextIndex() {
            return cursor;
        }

        public int previousIndex() {
            return cursor - 1;
        }

        @SuppressWarnings("unchecked")
        public E previous() {
            checkForComodification();
            int i = cursor - 1;
            if (i < 0)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i;
            return (E) elementData[lastRet = i];
        }

        public void set(E e) {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.set(lastRet, e);
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        public void add(E e) {
            checkForComodification();

            try {
                int i = cursor;
                ArrayList.this.add(i, e);
                cursor = i + 1;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }
```

### Fail-Fast机制

modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。

在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 ConcurrentModificationException。

如下例子：

```java
public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
        Iterator<Integer> iterator = list.listIterator();
        while (iterator.hasNext()) {
            Integer i = iterator.next();
            if (i == 1) {
                list.remove(i);
            }
        }
    }
```

运行后会抛出异常：

```java
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:886)
	at java.util.ArrayList$Itr.next(ArrayList.java:836)
	at MyTest.main(MyTest.java:12)
```

当我们调用list.remove的方法来删除元素后，此时modCount会+1，导致modCount和迭代器里面的expectedModCount 不相等，当遍历下一个元素调用next方法时，会先调用checkForComodification()方法，当expectedModCount！=modCount时会抛出ConcurrentModificationException，这就是Fail-Fast机制。

那我们要如何避免此问题呢？Iterator已经为我们提供了remove方法，所以我们只需要调用迭代器里面的remove方法就可以了，Iterator中的remove方法移除元素后会把modCount重写赋值给expectedModCount，下一个循环时expectedModCount与modCount相等就避免此问题。

如下例子：

```java
public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
        Iterator<Integer> iterator = list.listIterator();
        while (iterator.hasNext()) {
            Integer i = iterator.next();
            if (i == 1) {
                iterator.remove();
            }
        }
    }
```

### ArrayList序列化机制

我们看到ArrayList实现了Serializable接口，那么证明可以是被序列化的，但是elementData数组又被transient关键字修饰，我们知道被transient修饰的成员属性变量不被序列化，那么我们先看一个例子，ArrayList是否能被序列化成功呢？

```java
public static void main(String[] args) throws IOException, ClassNotFoundException {
        List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
        objectOutputStream.writeObject(list);

        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(byteArrayOutputStream.toByteArray());
        ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
        List<Integer> newList = (List<Integer>) objectInputStream.readObject();
        System.out.println(Arrays.toString(newList.toArray()));
    }
```

运行输出结果：

```java
[1, 2, 3]
```

结果是序列化和反序列化成功？？这是为什么呢？

其实细心的我们在查看源码时发现，ArrayList重写了readObject和writeObject来自定义的序列化和反序列化策略。什么是自定义序列化和反序列化呢？

在序列化过程中，如果被序列化的类中定义了writeObject 和 readObject 方法，虚拟机会试图调用对象类里的 writeObject 和 readObject 方法，进行用户自定义的序列化和反序列化。

如果没有这样的方法，则默认调用是 ObjectOutputStream 的 defaultWriteObject 方法以及 ObjectInputStream 的 defaultReadObject 方法。

用户自定义的 writeObject 和 readObject 方法可以允许用户控制序列化的过程，比如可以在序列化的过程中动态改变序列化的数值。

那么我们来看一下ArrayList源码是怎么来自定义序列化和反序列化的：

```java
private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }
private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }
```

可以看到通过writeObject方法和readObject方法来遍历elementData数组把数组中的元素写入ObjectOutputStream ，ObjectInputStream 中的。那么为什么ArrayList要用这种方式来实现序列化呢？

**ArrayList实际上是动态数组，每次在放满以后自动增长设定的长度值，如果数组自动增长长度设为100，而实际只放了一个元素，那就会序列化99个null元素。为了保证在序列化的时候不会将这么多null同时进行序列化，ArrayList把元素数组设置为transient。**

### ArrayList克隆机制

ArrayList的clone实现，其实是通过数组元素拷贝来实现的浅拷贝，很简单，我们看一下源码就行了：

```java
public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
```



## LinkedList

LinkedList类是List接口的实现类，它是一个集合，还实现了Deque接口，可以当成双端队列来使用。

虽然LinkedList是一个List集合，但是它的实现方式和ArrayList是完全不同的，ArrayList的底层是通过一个动态的Object[]数组实现的，而LinkedList的底层是通过双向链表来实现的，因此它的随机访问速度是比较差的，但是它的删除，插入操作很快。

LinkedList是基于双向循环链表实现的，除了可以当作链表操作外，它还可以当作栈、队列和双端队列来使用。

LinkedList同样是非线程安全的，只在单线程下适合使用。

LinkedList允许null插入。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

- 实现List接口，具有List集合的特性。
- 实现Deque接口，具有队列的特性。
- 实现Cloneable接口，可以通过clone来实现浅拷贝
- 实现Serializable接口，可以序列化，通过writeObject和readObject自定义序列化。

LinkedList的底层结构如下图所示：

![20191005112154463](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191005112154463.png)

F表示头结点引用，L表示尾结点引用，链表的每个结点都有三个元素，分别是前继结点引用(P)，结点元素的值(E)，后继结点的引用(N)。

结点由内部类Node表示，我们看看它的内部结构。

```java
private static class Node<E> {
        E item;//当前元素
        Node<E> next;//下一个节点
        Node<E> prev;//上一个节点

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

Node这个内部类其实很简单，只有三个成员变量和一个构造器，item表示结点的值，next为下一个结点的引用，prev为上一个结点的引用，通过构造器传入这三个值。接下来再看看LinkedList的成员变量和构造器。

```java
	/**
	* 集合元素个数
	*/
	transient int size = 0;

    /**
     * 头结点
     */
    transient Node<E> first;

    /**
     * 尾节点
     */
    transient Node<E> last;

    /**
     * 无参构造器
     */
    public LinkedList() {
    }

    /**
     * 传入外部集合的构造器
     */
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```

LinkedList持有头结点的引用和尾结点的引用，它有两个构造器，一个是无参构造器，一个是传入外部集合的构造器。与ArrayList不同的是LinkedList没有指定初始大小的构造器。

**LinkedList的List特性：**

```java
	//增
   /**
	* 将指定的元素追加到此列表的末尾
	*/
	public boolean add(E e) {
        linkLast(e);
        return true;
    }
   /**
	* 在此列表中的指定位置插入指定的元素。
	*/
	public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
   /**
	* 将指定集合中的所有元素追加到此列表的末尾。
	*/
	public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }
   /**
	* 将指定集合中的所有元素插入到此列表中，从指定的位置开始。
	*/
	public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        if (numNew == 0)
            return false;

        Node<E> pred, succ;
        if (index == size) {
            succ = null;
            pred = last;
        } else {
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
   /**
	* 头插入
	*/
	public void addFirst(E e) {
        linkFirst(e);
    }
   /**
	* 尾插入
	*/
	public void addLast(E e) {
        linkLast(e);
    }

	//删
   /**
	* 从列表中删除指定元素的第一个出现（如果存在）。
	*/
	public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
   /**
	* 删除该列表中指定位置的元素。
	*/
	public E remove(int index) {
        checkElementIndex(index);
        return unlink(node(index));
    }
   /**
	* 从此列表中删除并返回第一个元素。
	*/
	public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
    }
   /**
	* 从此列表中删除并返回最后一个元素。
	*/
	public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }

	//改
   /**
	* 用指定的元素替换此列表中指定位置的元素。
	*/
	public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }

	//查
   /**
	* 返回此列表中指定位置的元素。
	*/
	public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
```

LinkedList的添加元素的方法主要是调用linkLast和linkBefore两个方法，linkLast方法是在链表后面链接一个元素，linkBefore方法是在链表中间插入一个元素。LinkedList的删除方法通过调用unlink方法将某个元素从链表中移除。下面我们看看链表的插入和删除操作的核心代码。

```java
	/**
     * 返回指定位置的节点
     */
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
    /**
     * 连接到第一个元素
     */
    private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }

    /**
     * 链接到最后一个元素
     */
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }

    /**
     * 在succ前插入元素e
     */
    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }

    /**
     * 去掉头结点
     */
    private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }

    /**
     * 去掉尾结点
     */
    private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        final E element = l.item;
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }

    /**
     * 去掉指定的节点
     */
    E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
```

**LinkedList的Queue特性：**

通过对双向链表的操作还可以实现单项队列，双向队列和栈的功能。

单向队列操作：

```java
//获取头结点
public E peek() {
   final Node<E> f = first;
   return (f == null) ? null : f.item;
}

//获取头结点
public E element() {
   return getFirst();
}

//弹出头结点
public E poll() {
   final Node<E> f = first;
   return (f == null) ? null : unlinkFirst(f);
}

//移除头结点
public E remove() {
   return removeFirst();
}

//在队列尾部添加结点
public boolean offer(E e) {
   return add(e);
}
```

双向队列操作：

```java
//在头部添加
public boolean offerFirst(E e) {
   addFirst(e);
   return true;
}

//在尾部添加
public boolean offerLast(E e) {
   addLast(e);
   return true;
}

//获取头结点
public E peekFirst() {
   final Node<E> f = first;
   return (f == null) ? null : f.item;
}

//获取尾结点
public E peekLast() {
   final Node<E> l = last;
   return (l == null) ? null : l.item;
}
```

栈操作：

```java
//入栈
public void push(E e) {
   addFirst(e);
}

//出栈
public E pop() {
   return removeFirst();
}
```

不管是单向队列还是双向队列还是栈，其实都是对链表的头结点和尾结点进行操作，它们的实现都是基于addFirst()，addLast()，removeFirst()，removeLast()这四个方法。

- LinkedList是基于双向链表实现的，不论是增删改查方法还是队列和栈的实现，都可通过操作结点实现。
- LinkedList无需提前指定容量，因为基于链表操作，集合的容量随着元素的加入自动增加。
- LinkedList删除元素后集合占用的内存自动缩小，无需像ArrayList一样调用trimToSize()方法。
- LinkedList的所有方法没有进行同步，因此它也不是线程安全的，应该避免在多线程环境下使用。

LinkedList集合作为双端队列和栈的简单演示：

```java
public static void main(String[] args) {
    LinkedList<String> list = new LinkedList<String>();
    //将字符串元素加入到队列的尾部
    list.offer("java");
    //将该字符串元素加入到栈的栈的顶部（相当于队列的头部）
    list.push("c");
    for(String num : list){
        System.out.print(num);//输出cjava
    }
    //将该字符串元素加入到队列头部(相当于栈的顶部)
    list.offerFirst("c#");
    //访问并不删除队列头部元素
    System.out.println(list.peekFirst());//c#
    //将栈顶元素弹出栈(相当于删除队列头部元素)
    list.pop();
    for(String num : list){
        System.out.print(num);//输出cjava
    }
}
```



## Vector和Stack

### Vector

Vector 是线程安全的动态数组，同 ArrayList 一样继承自 AbstractList 且实现了 List、RandomAccess、Cloneable、Serializable 接口。

内部实现依然基于数组，Vector 与 ArrayList 基本是一致的，唯一不同的是 Vector 是线程安全的，会在可能出现线程安全的方法前面加上 synchronized 关键字。

其和 ArrayList 类似，随机访问速度快，插入和移除性能较差（数组原因），支持 null 元素，有顺序，元素可以重复，线程安全。

**变量以及初始化：**

```java
	/**
     * 存储元素数组
     */
    protected Object[] elementData;

    /**
     * 元素个数
     */
    protected int elementCount;

    /**
     * 容量增长的系数
     */
    protected int capacityIncrement;

    /** use serialVersionUID from JDK 1.0.2 for interoperability */
    private static final long serialVersionUID = -2767605614048989439L;

    /**
     * 指定容量和增长系数的构造函数
     */
    public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
        this.capacityIncrement = capacityIncrement;
    }

    /**
     * 指定容量构造函数
     */
    public Vector(int initialCapacity) {
        this(initialCapacity, 0);
    }

    /**
     * 空构造函数，默认容量10
     */
    public Vector() {
        this(10);
    }

    /**
     * 包含指定集合的构造函数
     */
    public Vector(Collection<? extends E> c) {
        elementData = c.toArray();
        elementCount = elementData.length;
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, elementCount, Object[].class);
    }
```

看一下Vector中自定义的readObject和writeObject:

```java
private void readObject(ObjectInputStream in)
            throws IOException, ClassNotFoundException {
        ObjectInputStream.GetField gfields = in.readFields();
        int count = gfields.get("elementCount", 0);
        Object[] data = (Object[])gfields.get("elementData", null);
        if (count < 0 || data == null || count > data.length) {
            throw new StreamCorruptedException("Inconsistent vector internals");
        }
        elementCount = count;
        elementData = data.clone();
    }

    private void writeObject(java.io.ObjectOutputStream s)
            throws java.io.IOException {
        final java.io.ObjectOutputStream.PutField fields = s.putFields();
        final Object[] data;
        synchronized (this) {
            fields.put("capacityIncrement", capacityIncrement);
            fields.put("elementCount", elementCount);
            data = elementData.clone();
        }
        fields.put("elementData", data);
        s.writeFields();
    }
```

**扩容：**

Vector 在 capacityIncrement 大于 0 时扩容 capacityIncrement 大小，否则扩容为原始容量的 2 倍，而ArrayList 在默认数组容量不够时默认扩展是 1.5 倍。

```java
	public synchronized void ensureCapacity(int minCapacity) {
        if (minCapacity > 0) {
            modCount++;
            ensureCapacityHelper(minCapacity);
        }
    }
	private void ensureCapacityHelper(int minCapacity) {
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
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

**增删改查**
Vector是JDK1.0时已经有的，而List框架是1.2时才出现的，所以Vector在List接口定义的增删改查以外还有他自己定义的增删改查方法，我们来看一下：

```java
	public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }
	public synchronized void addElement(E obj) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = obj;
    }


    public boolean remove(Object o) {
        return removeElement(o);
    }
    public synchronized boolean removeElement(Object obj) {
        modCount++;
        int i = indexOf(obj);
        if (i >= 0) {
            removeElementAt(i);
            return true;
        }
        return false;
    }
    public synchronized E remove(int index) {
        modCount++;
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);
        E oldValue = elementData(index);

        int numMoved = elementCount - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--elementCount] = null; // Let gc do its work

        return oldValue;
    }
    public synchronized void removeElementAt(int index) {
        modCount++;
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        else if (index < 0) {
            throw new ArrayIndexOutOfBoundsException(index);
        }
        int j = elementCount - index - 1;
        if (j > 0) {
            System.arraycopy(elementData, index + 1, elementData, index, j);
        }
        elementCount--;
        elementData[elementCount] = null; /* to let gc do its work */
    }
    
    
    public synchronized E set(int index, E element) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
    public synchronized void setElementAt(E obj, int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        elementData[index] = obj;
    }


	public synchronized E get(int index) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        return elementData(index);
    }
    public synchronized E elementAt(int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " + elementCount);
        }

        return elementData(index);
    }
```

增删改查方法基本上一样，所以Vector里面包含了大量实现相同的方法。

**迭代器实现**
Vector中迭代器实现和ArrayList中一样的，只不过Vector中为了保证线程安全，在方法体里面加了synchronized关键字。

```java
	private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        public boolean hasNext() {
            // Racy but within spec, since modifications are checked
            // within or after synchronization in next/previous
            return cursor != elementCount;
        }

        public E next() {
            synchronized (Vector.this) {
                checkForComodification();
                int i = cursor;
                if (i >= elementCount)
                    throw new NoSuchElementException();
                cursor = i + 1;
                return elementData(lastRet = i);
            }
        }

        public void remove() {
            if (lastRet == -1)
                throw new IllegalStateException();
            synchronized (Vector.this) {
                checkForComodification();
                Vector.this.remove(lastRet);
                expectedModCount = modCount;
            }
            cursor = lastRet;
            lastRet = -1;
        }

        @Override
        public void forEachRemaining(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            synchronized (Vector.this) {
                final int size = elementCount;
                int i = cursor;
                if (i >= size) {
                    return;
                }
        @SuppressWarnings("unchecked")
                final E[] elementData = (E[]) Vector.this.elementData;
                if (i >= elementData.length) {
                    throw new ConcurrentModificationException();
                }
                while (i != size && modCount == expectedModCount) {
                    action.accept(elementData[i++]);
                }
                // update once at end of iteration to reduce heap write traffic
                cursor = i;
                lastRet = i - 1;
                checkForComodification();
            }
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }

    /**
     * An optimized version of AbstractList.ListItr
     */
    final class ListItr extends Itr implements ListIterator<E> {
        ListItr(int index) {
            super();
            cursor = index;
        }

        public boolean hasPrevious() {
            return cursor != 0;
        }

        public int nextIndex() {
            return cursor;
        }

        public int previousIndex() {
            return cursor - 1;
        }

        public E previous() {
            synchronized (Vector.this) {
                checkForComodification();
                int i = cursor - 1;
                if (i < 0)
                    throw new NoSuchElementException();
                cursor = i;
                return elementData(lastRet = i);
            }
        }

        public void set(E e) {
            if (lastRet == -1)
                throw new IllegalStateException();
            synchronized (Vector.this) {
                checkForComodification();
                Vector.this.set(lastRet, e);
            }
        }

        public void add(E e) {
            int i = cursor;
            synchronized (Vector.this) {
                checkForComodification();
                Vector.this.add(i, e);
                expectedModCount = modCount;
            }
            cursor = i + 1;
            lastRet = -1;
        }
    }
```

**克隆实现**
Vector克隆实现和ArrayList的实现一致，都是通过数组元素拷贝来实现的浅拷贝。

```java
public synchronized Object clone() {
        try {
            @SuppressWarnings("unchecked")
                Vector<E> v = (Vector<E>) super.clone();
            v.elementData = Arrays.copyOf(elementData, elementCount);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
```

为什么现在都不提倡使用 Vector 了？

因为 Vector 实现并发安全的原理是在每个操作方法上加锁，这些锁并不是必须要的，在实际开发中一般都是通过锁一系列的操作来实现线程安全，也就是说将需要同步的资源放一起加锁来保证线程安全，如果多个 Thread 并发执行一个已经加锁的方法，但是在该方法中又有 Vector 的存在，Vector 本身实现中已经加锁了，双重锁会造成额外的开销，即 Vector 同 ArrayList 一样有 fail-fast 问题（即无法保证遍历安全），所以在遍历 Vector 操作时又得额外加锁保证安全，还不如直接用 ArrayList 加锁性能好，所以在 JDK 1.5 之后推荐使用 java.util.concurrent 包下的并发类。此外 Vector 是一个从 JDK1.0 就有的古老集合，那时候 Java 还没有提供系统的集合框架，所以在 Vector 里提供了一些方法名很长的方法（如 addElement(Object obj)，实际上这个方法和 add(Object obj) 没什么区别），从 JDK1.2 以后 Java 提供了集合框架，然后就将 Vector 改为实现 List 接口，从而导致 Vector 里有一些重复的方法。

### Stack

通过继承Vector类，Stack类可以很容易的实现他本身的功能。因为大部分的功能在Vector里面已经提供支持了。

在Java中Stack类表示后进先出（LIFO）的对象堆栈。栈是一种非常常见的数据结构，它采用典型的先进后出的操作方式完成的。

Stack通过五个操作对Vector进行扩展，允许将向量视为堆栈。这个五个操作如下：

1. empty() 测试堆栈是否为空。
2. peek() 查看堆栈顶部的对象，但不从堆栈中移除它。
3. pop() 移除堆栈顶部的对象，并作为此函数的值返回该对象。
4. push(E item) 把项压入堆栈顶部。
5. search(Object o) 返回对象在堆栈中的位置，以 1 为基数。

源码：

```java

public class Stack<E> extends Vector<E> {
    /**
     * Creates an empty Stack.
     */
    public Stack() {
    }

    /**
     * 将元素存入栈顶
     */
    public E push(E item) {
        addElement(item);

        return item;
    }

    /**
     * 返回栈顶元素，并将其从栈中删除
     */
    public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }

    /**
     * 返回栈顶元素，不执行删除操作
     */
    public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }

    /**
     * 栈是否为空
     */
    public boolean empty() {
        return size() == 0;
    }

    /**
     * 查找“元素o”在栈中的位置：由栈底向栈顶方向数
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



## HashSet、LinkedHashSet和TreeSet

### HashSet

```java
public class HashSet<E> extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
```

- HashSet 继承于AbstractSet 该类提供了Set 接口的骨架实现，以最大限度地减少实现此接口所需的工作量。
- 实现Set接口，标志着内部元素是无序的，元素是不可以重复的。
- 实现Cloneable接口，标识着可以它可以被复制。
- 实现Serializable接口，标识着可被序列化。

HashSet内部是以HashMap的key来保存元素的：

```java
	//使用HashMap的Key来保存HashSet中所有元素。
	private transient HashMap<E,Object> map;

    //定义一个Object对象作为HashMap的value
    private static final Object PRESENT = new Object();
```

构造函数：

```java
   /**
	* 构造一个空的hashSet，实际底层会初始化一个空的HashMap，并使用默认初始容量为16和加载因子0.75。
	*/
	public HashSet() {
        map = new HashMap<>();
    }

    /**
     * 构造一个包含指定集合中元素的新集合。HashMap是使用默认加载因子（0.75）和足以包含指定
     * 集合中元素的初始容量创建的。
     */
    public HashSet(Collection<? extends E> c) {
        map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
        addAll(c);
    }

    /**
     * 以指定的initialCapacity和loadFactor构造一个空的HashSet
     * 实际底层以相应的参数构造一个空的HashMap。
     */
    public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    }

    /**
     * 以指定的initialCapacity构造一个空的HashSet。
     * 实际底层以相应的参数及加载因子loadFactor为0.75构造一个空的HashMap。
     */
    public HashSet(int initialCapacity) {
        map = new HashMap<>(initialCapacity);
    }

    /**
     * 以指定的initialCapacity和loadFactor构造一个新的空链接哈希集合。
     * 此构造函数为包访问权限，不对外公开，实际只是是对LinkedHashSet的支持。
     * 实际底层会以指定的参数构造一个空LinkedHashMap实例来实现。
     */
    HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
```

迭代器实现：返回key的集合的迭代器

```java
    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }
```

```java
	/**
     * 返回此set中的元素的数量（set的容量）。
     * 底层实际调用HashMap的size()方法返回Entry的数量，就得到该Set中元素的个数。
     */
    public int size() {
        return map.size();
    }

    /**
     * 如果此set不包含任何元素，则返回true。 
     * 底层实际调用HashMap的isEmpty()判断该HashSet是否为空。
     */
    public boolean isEmpty() {
        return map.isEmpty();
    }

    /**
     * 如果此set包含指定元素，则返回true。
     * 底层实际调用HashMap的containsKey判断是否包含指定key。
     */
    public boolean contains(Object o) {
        return map.containsKey(o);
    }

    /**
     * 如果此set中尚未包含指定元素，则添加指定元素。
     * 底层实际将将该元素作为key放入HashMap。
     */
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }

    /**
     * 如果指定元素存在于此set中，则将其移除。 
     * 底层实际调用HashMap的remove方法删除指定Entry。
     */
    public boolean remove(Object o) {
        return map.remove(o)==PRESENT;
    }

    /**
     * 从此set中移除所有元素。
     * 底层实际调用HashMap的clear方法清空Entry中所有元素。
     */
    public void clear() {
        map.clear();
    }

    /**
     * 返回此HashSet实例的浅表副本：并没有复制这些元素本身。 
     * 底层实际调用HashMap的clone()方法，获取HashMap的浅表副本，并设置到HashSet中。
     */
    @SuppressWarnings("unchecked")
    public Object clone() {
        try {
            HashSet<E> newSet = (HashSet<E>) super.clone();
            newSet.map = (HashMap<E, Object>) map.clone();
            return newSet;
        } catch (CloneNotSupportedException e) {
            throw new InternalError(e);
        }
    }

    /**
     * 自定义序列化实现
     */
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        // Write out any hidden serialization magic
        s.defaultWriteObject();

        // Write out HashMap capacity and load factor
        s.writeInt(map.capacity());
        s.writeFloat(map.loadFactor());

        // Write out size
        s.writeInt(map.size());

        // Write out all elements in the proper order.
        for (E e : map.keySet())
            s.writeObject(e);
    }
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden serialization magic
        s.defaultReadObject();

        // Read capacity and verify non-negative.
        int capacity = s.readInt();
        if (capacity < 0) {
            throw new InvalidObjectException("Illegal capacity: " +
                                             capacity);
        }

        // Read load factor and verify positive and non NaN.
        float loadFactor = s.readFloat();
        if (loadFactor <= 0 || Float.isNaN(loadFactor)) {
            throw new InvalidObjectException("Illegal load factor: " +
                                             loadFactor);
        }

        // Read size and verify non-negative.
        int size = s.readInt();
        if (size < 0) {
            throw new InvalidObjectException("Illegal size: " +
                                             size);
        }
        capacity = (int) Math.min(size * Math.min(1 / loadFactor, 4.0f),
                HashMap.MAXIMUM_CAPACITY);
        SharedSecrets.getJavaOISAccess()
                     .checkArray(s, Map.Entry[].class, HashMap.tableSizeFor(capacity));

        // Create backing HashMap
        map = (((HashSet<?>)this) instanceof LinkedHashSet ?
               new LinkedHashMap<E,Object>(capacity, loadFactor) :
               new HashMap<E,Object>(capacity, loadFactor));

        // Read in all elements in the proper order.
        for (int i=0; i<size; i++) {
            @SuppressWarnings("unchecked")
                E e = (E) s.readObject();
            map.put(e, PRESENT);
        }
    }

    public Spliterator<E> spliterator() {
        return new HashMap.KeySpliterator<E,Object>(map, 0, -1, 0, 0);
    }
```

- HashSet就是限制了功能的HashMap，所以了解HashMap的实现原理，这个HashSet自然就通。
- 对于HashSet中保存的对象，主要要正确重写equals方法和hashCode方法，以保证放入Set对象的唯一性。
- 虽说Set是对于重复的元素不放入，倒不如直接说是底层的Map直接把原值替代了（这个Set的put方法的返回值真有意思）。
- HashSet没有提供get()方法，是同HashMap一样，Set内部是无序的，只能通过迭代的方式获得。



### LinkedHashSet

LinkedHashSet是HashSet的一个“扩展版本”，HashSet并不管什么顺序，不同的是LinkedHashSet会维护“插入顺序”。HashSet内部使用HashMap对象来存储它的元素，而LinkedHashSet内部使用LinkedHashMap对象来存储和处理它的元素。
源码如下：

```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {

    private static final long serialVersionUID = -2851667679971038690L;

    public LinkedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor, true);
    }
    
    public LinkedHashSet(int initialCapacity) {
        super(initialCapacity, .75f, true);
    }

    public LinkedHashSet() {
        super(16, .75f, true);
    }

    public LinkedHashSet(Collection<? extends E> c) {
        super(Math.max(2*c.size(), 11), .75f, true);
        addAll(c);
    }

    @Override
    public Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.DISTINCT | Spliterator.ORDERED);
    }
}
```

从源码中我们可以注意到，LinkedHashSet继承于HashSet，只包含4个构造函数，这4个构造函数调用的是同一个父类的构造函数。我们来看一下父类中的这个构造函数：

```java
HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
```

这个构造函数需要初始容量，负载因子和一个boolean类型的哑值（没有什么用处的参数，作为标记，译者注）等参数。这个哑参数只是用来区别这个构造函数与HashSet的其他拥有初始容量和负载因子参数的构造函数。

这个构造函数内部初始化了一个LinkedHashMap对象，这个对象恰好被LinkedHashSet用来存储它的元素。

LinkedHashSet并没有自己的方法，所有的方法都继承自它的父类HashSet，因此，对LinkedHashSet的所有操作方式就好像对HashSet操作一样。

唯一的不同是内部使用不同的对象去存储元素。在HashSet中，插入的元素是被当做HashMap的键来保存的，而在LinkedHashSet中被看作是LinkedHashMap的键。



### TreeSet

我们知道TreeMap是一个有序的二叉树，那么同理TreeSet同样也是一个有序的，它的作用是提供有序的Set集合。TreeSet中的元素支持2种排序方式：自然排序 或者 根据创建TreeSet 时提供的 Comparator 进行排序。这取决于使用的构造方法。

通过源码我们知道TreeSet基础AbstractSet，实现NavigableSet、Cloneable、Serializable接口。

其中AbstractSet提供 Set 接口的骨干实现，从而最大限度地减少了实现此接口所需的工作。

NavigableSet是扩展的 SortedSet，具有了为给定搜索目标报告最接近匹配项的导航方法，这就意味着它支持一系列的导航方法。比如查找与指定目标最匹配项。Cloneable支持克隆，Serializable支持序列化。

```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
    //使用NavigableMap来保存TreeSet元素
    private transient NavigableMap<E,Object> m;

    // 与NavigableMap中的对象关联的虚拟值
    private static final Object PRESENT = new Object();

    /**
     * 构造由指定的NavigableMap的集合。
     */
    TreeSet(NavigableMap<E,Object> m) {
        this.m = m;
    }

    /**
     * 构造一个新的空TreeSet，根据元素的自然排序进行排序。 插入到集合中的所有元素都必须实现Comparable接口。 
     * 此外，所有这些元素必须可以相互比较 如果用户尝试向违反此约束的集合添加元素，那么add调用将抛出一个
     * ClassClassException。
     */
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }

    /**
     * 构造一个新的空TreeSet，根据指定的比较器进行排序。 插入到集合中的所有元素必须与指定的比较器可相互比较
     * 如果用户尝试向违反此约束的集合添加元素，那么add调用将抛出ClassCastException。
     */
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }

    /**
     *构造一个新的TreeSet，其中包含指定集合中的元素，并根据元素的 自然排序 进行排序。 
     *插入到集合中的所有元素都必须实现 Comparable接口。 此外，所有这些元素必须可以相互比较
     */
    public TreeSet(Collection<? extends E> c) {
        this();
        addAll(c);
    }

    /**
     * 构造一个包含相同元素并使用与指定的排序集相同顺序的TreeSet。
     */
    public TreeSet(SortedSet<E> s) {
        this(s.comparator());
        addAll(s);
    }

    /**
     * 以升序返回此集合中元素的迭代器。
     */
    public Iterator<E> iterator() {
        return m.navigableKeySet().iterator();
    }

    /**
     * 以降序返回此集合中元素的迭代器。
     */
    public Iterator<E> descendingIterator() {
        return m.descendingKeySet().iterator();
    }

    /**
     * @since 1.6
     */
    public NavigableSet<E> descendingSet() {
        return new TreeSet<>(m.descendingMap());
    }

    /**
     * 返回此集合中元素的数量（基数）。返回此集合中元素的数量。
     */
    public int size() {
        return m.size();
    }

    /**
     * 返回TreeSet是否为空
     */
    public boolean isEmpty() {
        return m.isEmpty();
    }

    /**
     * 返回TreeSet是否包含对象(o)
     */
    public boolean contains(Object o) {
        return m.containsKey(o);
    }

    /**
     * 添加e到TreeSet中
     */
    public boolean add(E e) {
        return m.put(e, PRESENT)==null;
    }

    /**
     * 删除TreeSet中的对象o
     */
    public boolean remove(Object o) {
        return m.remove(o)==PRESENT;
    }

    /**
     * 清空TreeSet
     */
    public void clear() {
        m.clear();
    }

    /**
     * 将集合c中的全部元素添加到TreeSet中
     */
    public  boolean addAll(Collection<? extends E> c) {
        // Use linear-time version if applicable
        if (m.size()==0 && c.size() > 0 &&
            c instanceof SortedSet &&
            m instanceof TreeMap) {
            SortedSet<? extends E> set = (SortedSet<? extends E>) c;
            TreeMap<E,Object> map = (TreeMap<E, Object>) m;
            Comparator<?> cc = set.comparator();
            Comparator<? super E> mc = map.comparator();
            if (cc==mc || (cc != null && cc.equals(mc))) {
                map.addAllForTreeSet(set, PRESENT);
                return true;
            }
        }
        return super.addAll(c);
    }

    /**
     *  返回子Set，实际上是通过TreeMap的subMap()实现的。
     */
    public NavigableSet<E> subSet(E fromElement, boolean fromInclusive,
                                  E toElement,   boolean toInclusive) {
        return new TreeSet<>(m.subMap(fromElement, fromInclusive,
                                       toElement,   toInclusive));
    }

    /**
     * 返回Set的头部，范围是：从头部到toElement。
     * inclusive是是否包含toElement的标志
     */
    public NavigableSet<E> headSet(E toElement, boolean inclusive) {
        return new TreeSet<>(m.headMap(toElement, inclusive));
    }

    /**
     * 返回Set的尾部，范围是：从fromElement到结尾。
     * inclusive是是否包含fromElement的标志
     */
    public NavigableSet<E> tailSet(E fromElement, boolean inclusive) {
        return new TreeSet<>(m.tailMap(fromElement, inclusive));
    }

    /**
     * 返回子Set。范围是：从fromElement(包括)到toElement(不包括)。
     */
    public SortedSet<E> subSet(E fromElement, E toElement) {
        return subSet(fromElement, true, toElement, false);
    }

    /**
     * 返回Set的头部，范围是：从头部到toElement(不包括)。
     */
    public SortedSet<E> headSet(E toElement) {
        return headSet(toElement, false);
    }

    /**
     * 返回Set的尾部，范围是：从fromElement到结尾(不包括)。
     */
    public SortedSet<E> tailSet(E fromElement) {
        return tailSet(fromElement, true);
    }

    //返回Set的比较器
    public Comparator<? super E> comparator() {
        return m.comparator();
    }

    /**
     * 返回Set的第一个元素
     */
    public E first() {
        return m.firstKey();
    }

    /**
     * 返回Set的最后一个元素
     */
    public E last() {
        return m.lastKey();
    }

    // NavigableSet API methods

    /**
     * 返回Set中小于e的最大元素
     */
    public E lower(E e) {
        return m.lowerKey(e);
    }

    /**
     *返回Set中小于/等于e的最大元素
     */
    public E floor(E e) {
        return m.floorKey(e);
    }

    /**
     *返回Set中大于/等于e的最小元素
     */
    public E ceiling(E e) {
        return m.ceilingKey(e);
    }

    /**
     * 返回Set中大于e的最小元素
     */
    public E higher(E e) {
        return m.higherKey(e);
    }

    /**
     * 获取第一个元素，并将该元素从TreeMap中删除。
     */
    public E pollFirst() {
        Map.Entry<E,?> e = m.pollFirstEntry();
        return (e == null) ? null : e.getKey();
    }

    /**
     * 获取最后一个元素，并将该元素从TreeMap中删除。
     */
    public E pollLast() {
        Map.Entry<E,?> e = m.pollLastEntry();
        return (e == null) ? null : e.getKey();
    }

    /**
     *克隆一个TreeSet，并返回Object对象
     */
    @SuppressWarnings("unchecked")
    public Object clone() {
        TreeSet<E> clone;
        try {
            clone = (TreeSet<E>) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new InternalError(e);
        }

        clone.m = new TreeMap<>(m);
        return clone;
    }

    /**
     * java.io.Serializable的写入函数
     *将TreeSet的“比较器、容量，所有的元素值”都写入到输出流中
     */
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        // Write out any hidden stuff
        s.defaultWriteObject();

        // Write out Comparator
        s.writeObject(m.comparator());

        // Write out size
        s.writeInt(m.size());

        // Write out all elements in the proper order.
        for (E e : m.keySet())
            s.writeObject(e);
    }

    /**
     *  java.io.Serializable的读取函数：根据写入方式读出
     *  先将TreeSet的“比较器、容量、所有的元素值”依次读出
     */
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden stuff
        s.defaultReadObject();

        // Read in Comparator
        @SuppressWarnings("unchecked")
            Comparator<? super E> c = (Comparator<? super E>) s.readObject();

        // Create backing TreeMap
        TreeMap<E,Object> tm = new TreeMap<>(c);
        m = tm;

        // Read in size
        int size = s.readInt();

        tm.readTreeSet(size, s, PRESENT);
    }
    public Spliterator<E> spliterator() {
        return TreeMap.keySpliteratorFor(m);
    }

    private static final long serialVersionUID = -2479143000061671589L;
}
```

TreeSet实际上是TreeMap实现的。当我们构造TreeSet时，若使用不带参数的构造函数，则TreeSet使用自然比较器；若用户需要使用自定义的比较器，则需要使用带比较器的参数。



## PriorityQueue

参考链接：

[(27条消息) 吃透Java集合系列七：PriorityQueue_吃透Java-CSDN博客](https://blog.csdn.net/u013277209/article/details/102468399)



## Map

**Map接口分析**
关于Map接口，JDK是这样描述的：

Map是一个有键值对映射的对象，map不能包含相同的key，每一个key至多能映射一个value。

Map替代了Dictionary这个类，Dictionary是抽象类而非接口，替代原因：接口总是优于抽象类。

Map接口提供了3个集合视图，包括:keys的set集合， values的集合;，key-value的set集合，注意:values集合不是set类型,因为value可相同。

Map返回元素的顺序，取决于map对应的某个集合视图迭代器的顺序，一些map的实现，比如TreeMap类，对于map返回元素的顺序有特殊的规定，其它的map实现类,比如HashMap类,就没有特殊的规定。

不要把异变的对象作为Map的key，如果map中的一个key发生了改变，并且影响了equals()方法的使用，那么map并不会提示我们。

所有的通用map实现类都应该提供两个"标准"的构造器函数，一个是无参且返回类型为void的函数，另一个就是仅含有一个参数且类型为map类型的的构造方法,这个方法会使用其参数构造一个新的map，且新的map和参数map有相同的key和value，但是，map接口这里没办法强制执行这一建议(因为接口里面不能包含构造器函数)，不过JDK里面所有通用的map实现类都是符合这一点要求的。

一些map的实现类在key和value的取值上面会有一些规定，比如，一些map实现类不允许key或者value为null。

map接口是java集合框架中的一个成员。

集合框架接口定义了很多种和equals()方法相关的实现，比如,containsKey()方法，当前仅当map包含了键k的定义是：key = = null ? k == null : key.equals(k)，这一规范的写法，不能被理解为为:如果调用方法使用的是一个非null参数的话,然后只是再调用key.equals(k)方法就可以了，具体实现可以通过避免调用equals()方法来实现优化，比如，可以先比较两个key的哈希值，(哈希值保证了，如果两个对象的哈希值都不相同，那么这两个对象肯定不会相同)，更一般的情况是，大量集合框架接口的实现类可以充分利用底层对象(Object)的方法的优势，只要实现者认为他们这么做是合理的。

```java
public interface Map<K,V> {
	//增
	/**
     * put方法是将指定的key-value存储到map里面的操作.如果map之前包含了一个此key对应的映射,
     * 那么此key对应的旧value值会被新的value值替换.
     */
    V put(K key, V value);
    /**
     * putAll方法是将一个指定map的映射拷贝到当前map.这一操作类似于将指定map的key-value对通过put方法一个个拷贝过来。
     * 在拷贝过程中,如果指定的这个map被更改了,那么这时候会出现什么情况,并不清楚。
     */
    void putAll(Map<? extends K, ? extends V> m);
	
	//删
	/**
     * remove方法用于移除map中已有的某个key.更一般的讲,如果map包含了一个满足条件key==null ?  k==null : key.equals(k)的映射,
     * 这一映射就会被移除.(map最多包含一个这样的映射)
     * 本方法会返回移除的key对应的value值,如果map这个key没有对应的value值,则返回null。
     * 如果map允许null值,那么返回null值并不一定表明map不包含对应key的映射值;因为这也可能是key本身对应的value值就是null.
	 */
    V remove(Object key);
    /**
     * 移除map中所有的映射
     */
    void clear();

	//查
	/**
     * 返回map中key-value映射的个数.如果map包含的key-value个数超过了Integer.MAX_VALUE这个数
     * 则返回Integer.MAX_VALUE.
     */
    int size();

    /**
     * 如果map没有存储任何key-value,则返回true
     */
    boolean isEmpty();
    /**
     * 如果map存储了指定的key,则返回true.更一般的情况是,当且仅当map包含了一个key的映射
     * 映射情况是:key==null ? k==null : key.equals(k),此时返回true.
     */
    boolean containsKey(Object key);

    /**
     * 如果map中至少有一个key能映射到指定的value,那么就返回true.更一般的情况是,
     * 当且仅当value==null ? v==null : value.equals(v)条件成立,才返回true.
     * 在所有map接口的实现类中,这一操作都需要map大小的线性时间来完成.
     */
    boolean containsValue(Object value);

    /**
     * 返回指定key映射的value.如果map没有指定的key,则返回null.
     */
    V get(Object key);

	//三个返回key，value，key-value的集合
    /**
     * 此方法:返回map包含所有的key的一个set集合视图
     */
    Set<K> keySet();
    /**
     * values方法返回map内存储的所有值的集合(毕竟值集合中,值可以有重复的,所以此方法和上面的返回的
     * key集合的结果类型不一样,因为key肯定都是不同的).
     */
    Collection<V> values();

    /**
     * 此方法返回map里存储的所有映射的视图.
     */
    Set<Map.Entry<K, V>> entrySet();

	//两个覆盖object的方法
	 /**
     * 用于对比两个map是否相等.
     * 如果给定的对象是一个map且两个map的映射一致,则返回true.
     * 一般,两个map的映射一致,要满足的条件是:m1.entrySet().equals(m2.entrySet())
     * 这就保证了实现了map接口的不同类对于equals方法的使用才是正确的.
     */
    boolean equals(Object o);

    /**
     * 返回map的哈希值，map的哈希值被定义为:这个map的entrySet视图的每一个条目的哈希值的总和.
     * 这就保证了任意两个map相等,则他们的哈希值一定相等,这也是Object类对哈希值的普遍要求(哈希值作为两个对象相等的必要非充分条件).
     */
    int hashCode();

	/**
	 * map条目(key-value对),Map.entrySet方法返回的就是map的集合视图,map视图中的元素就是来源于此类.
	 * 获取map条目的唯一方式就是来源于集合视图的迭代器.只有在迭代的过程中,Map.Entry对象才是有效的;
	 * 通常,如果通过迭代器获得的map条目,在遍历过程中,作为后台支持的map被修改了,那么map条目会如何被影响,对此
	 * 并没有做出具体规定(当然此处说的map修改不包括setValue方法的调用).
	 */
	interface Entry<K,V> {
        /**
         * 获取当前map条目对应的key
         */
        K getKey();

        /**
         * 返回map条目对应的value值.
         */
        V getValue();

        /**
         * 用指定值替换当前条目中的value
         */
        V setValue(V value);

        /**
         * 将指定对象和当前条目做比较,如果给定的对象是一个条目并且两个条目代表同一个映射,则返回true.
         */
        boolean equals(Object o);

        /**
         * 返回map条目的哈希值
         */
        int hashCode();

		/**
         * 返回一个比较map.entry的比较器,按照key的自然顺序排序，返回的比较器支持序列化
         * @since 1.8
         */
        public static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getKey().compareTo(c2.getKey());
        }

        /**
         * 返回一个map.enty的比较器,按照value的自然顺序排序，返回的比较器支持序列化
         * @since 1.8
         */
        public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getValue().compareTo(c2.getValue());
        }

        /**
         * 返回一个map.entry的比较器,根据传入比较器对key排序，如果传入的比较器支持序列化,则返回的结果比较器也支持序列化.
         * @since 1.8
         */
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
        }

        /**
         * 返回一个map.entry的比较器,根据传入比较器对value排序，如果传入的比较器支持序列化,则返回的结果比较器也支持序列化
         * @since 1.8
         */
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
        }
    }

	
	//1.8新增
    /**
     * 返回指定key对应的value,如果没有对应的映射,则返回传入参数中的默认值defaultValue
     * @since 1.8
     */
    default V getOrDefault(Object key, V defaultValue) {
        V v;
        return (((v = get(key)) != null) || containsKey(key))
            ? v
            : defaultValue;
    }

    /**
     * 对map中每一个entry执行action中定义对操作,直到全部entry执行完成or执行中出现异常为止
     * @since 1.8
     */
    default void forEach(BiConsumer<? super K, ? super V> action) {
        Objects.requireNonNull(action);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
            action.accept(k, v);
        }
    }

    /**
     * 对于map中每一个entry,将其value替换成BiFunction接口返回的值.直到所有entry替换完or出现异常为止
     * @since 1.8
     */
    default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        Objects.requireNonNull(function);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }

            // ise thrown from function is not a cme.
            v = function.apply(k, v);

            try {
                entry.setValue(v);
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
        }
    }

    /**
     * 如果指定的键尚未与值相关联（或被映射为null）,则将它与给定的值相关联并返回null，否则返回当前值。
     * @since 1.8
     */
    default V putIfAbsent(K key, V value) {
        V v = get(key);
        if (v == null) {
            v = put(key, value);
        }

        return v;
    }

    /**
     * 如果给定的参数key和value在map中是一个entry,则删除这个entry.
     * @since 1.8
     */
    default boolean remove(Object key, Object value) {
        Object curValue = get(key);
        if (!Objects.equals(curValue, value) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        remove(key);
        return true;
    }

    /**
     * 如果给定的key和value在map中有entry,则为指定key的entry,用新value替换旧的value
     * @since 1.8
     */
    default boolean replace(K key, V oldValue, V newValue) {
        Object curValue = get(key);
        if (!Objects.equals(curValue, oldValue) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        put(key, newValue);
        return true;
    }

    /**
     *如果指定key在map中有value,则用参数value进行替换
     * @since 1.8
     */
    default V replace(K key, V value) {
        V curValue;
        if (((curValue = get(key)) != null) || containsKey(key)) {
            curValue = put(key, value);
        }
        return curValue;
    }

    /**
     * 如果指定key在map中没有对应的value,则使用输入参数,即函数接口mappingfunction为其计算一个value.
     * 如果计算value不为null,则将value插入map中，如果计算function返回结果为null,则不插入任何映射。
     * @since 1.8
     */
    default V computeIfAbsent(K key,
            Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        if ((v = get(key)) == null) {
            V newValue;
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
    }

    /**
     * 如果map中存在指定key对应的value,且不为null,则本方法会尝试使用function,并利用key生成一个新的value
     * 如果function接口返回null,map中原entry则被移除.如果function本身抛出异常,则当前map不会发生改变.
     * @since 1.8
     */
    default V computeIfPresent(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue;
        if ((oldValue = get(key)) != null) {
            V newValue = remappingFunction.apply(key, oldValue);
            if (newValue != null) {
                put(key, newValue);
                return newValue;
            } else {
                remove(key);
                return null;
            }
        } else {
            return null;
        }
    }

    /**
     * 利用指定key和value计算一个新映射，比如:向一个value映射中新增或者拼接一个String。
     * 如果function接口返回null,则map中原entry被移除(如果本来就不存在,则不执行移除操作).
     * 如果function本身抛出(非检查型)异常,异常会被重新抛出,当前map也不会发生改变.
     * @since 1.8
     */
    default V compute(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue = get(key);

        V newValue = remappingFunction.apply(key, oldValue);
        if (newValue == null) {
            // delete mapping
            if (oldValue != null || containsKey(key)) {
                // something to remove
                remove(key);
                return null;
            } else {
                // nothing to do. Leave things as they were.
                return null;
            }
        } else {
            // add or replace old mapping
            put(key, newValue);
            return newValue;
        }
    }

    /**
     * 如果指定key没有value,或者其value为null,则将其改为给定的非null的value
     * 否则,用给定的function返回值替换原value.
     * 如果给定参数value和function返回结果都为null,则删除map中这个entry.
     * 这一方法常用于:对一个key合并多个映射的value时.
     * 比如:要创建或追加一个String给一个值映射.
     * 如果function返回null,则map中原entry被移除.如果function本身抛出异常,则异常会被重新抛出,且当前map不会发生更改.
     * @since 1.8
     */
    default V merge(K key, V value,
            BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        V oldValue = get(key);
        V newValue = (oldValue == null) ? value :
                   remappingFunction.apply(oldValue, value);
        if(newValue == null) {
            remove(key);
        } else {
            put(key, newValue);
        }
        return newValue;
    }
}
    
}
```

上述注释为按照JDK进行翻译。



## HashMap

HashMap是由Hash表来实现的，数组+链表（1.8加入红黑树）的方式实现的，通过key的hash值与数组长度取余来获取应插入数组的下标，如果产生Hash冲突，在原下标位置转为链表，当链表长度到达8并且数组长度大于等于64则转为红黑树。

**什么是Hash表**

我们知道数组的特点是：寻址容易，插入和删除困难。

链表的特点是：寻址困难，插入和删除容易。

那么我们能不能综合两者的特性，做出一种寻址容易，插入删除也容易的数据结构？答案是肯定的，这就是我们要提起的哈希表，哈希表有多种不同的实现方法，HashMap中最常用的一种方法——拉链法，我们可以理解为“链表的数组”，如图：

![20191014165258624](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191014165258624.png)

**JDK1.8为什么引入红黑树？**

Hash算法设计的再合理，也免不了会出现拉链过长的情况，一旦出现拉链过长，则会严重影响HashMap的性能。于是，在JDK1.8版本中，对数据结构做了进一步的优化，引入了红黑树。而当链表长度太长（默认超过8）时，链表就转换为红黑树，利用红黑树快速增删改查的特点提高HashMap的性能，其中会用到红黑树的插入、删除、查找等算法。
深入了解红黑树请移步这里https://blog.csdn.net/v_july_v/article/details/6105630

**用什么方式解决Hash冲突？**

解决Hash冲突方法有：开放地址法（线性探测再散列，二次探测再散列，伪随机探测再散列）、再哈希法、链地址法、建立公共溢出区。

HashMap使用链地址法来解决Hash冲突。

**字段信息**

```java
transient Node<K,V>[] table;
transient int size;
transient int modCount;
int threshold;
final float loadFactor;
```

table是Hash表的数组结构，初始默认大小为16，里面存储Node信息

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;    //用来定位数组索引位置
        final K key;
        V value;
        Node<K,V> next;   //链表的下一个node

        Node(int hash, K key, V value, Node<K,V> next) { ... }
        public final K getKey(){ ... }
        public final V getValue() { ... }
        public final String toString() { ... }
        public final int hashCode() { ... }
        public final V setValue(V newValue) { ... }
        public final boolean equals(Object o) { ... }
}
```

Node是HashMap的一个内部类，实现了Map.Entry接口，本质是就是一个映射(键值对)。

Load factor为负载因子(默认值是0.75)，threshold是HashMap所能容纳的最大数据量的Node(键值对)个数。threshold = length * Load factor。也就是说，在数组定义好长度之后，负载因子越大，所能容纳的键值对个数越多。

threshold就是在此Load factor和length(数组长度)对应下允许的最大元素数目，超过这个数目就重新resize(扩容)，扩容后的HashMap容量是之前容量的两倍。默认的负载因子0.75是对空间和时间效率的一个平衡选择，建议不要修改，除非在时间和空间比较特殊的情况下，如果内存空间很多而又对时间效率要求很高，可以降低负载因子Load factor的值；相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子loadFactor的值，这个值可以大于1。

size是HashMap中实际存在的键值对数量，而modCount字段主要用来记录HashMap内部结构发生变化的次数，主要用于迭代的快速失败。强调一点，内部结构发生变化指的是结构发生变化，例如put新键值对，但是某个key对应的value值被覆盖不属于结构变化。

**确定哈希桶数组索引的位置**

不管增加、删除、查找键值对，定位到哈希桶数组的位置都是很关键的第一步。前面说过HashMap的数据结构是数组和链表的结合，所以我们当然希望这个HashMap里面的元素位置尽量分布均匀些，尽量使得每个位置上的元素数量只有一个，那么当我们用hash算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们要的，不用遍历链表，大大优化了查询的效率。HashMap定位数组索引位置，直接决定了hash方法的离散性能。先看看源码的实现：

```java
//取Hash值，并且高位运算
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
//定位下标
tab[(n - 1) & hash]；
```

**这里的Hash算法本质上就是三步：取key的hashCode值、高位运算、取模运算。**
那么问题来了
**1、为什么要异或呢？为什么要移位呢？而且移位16？**

```shell
我们来看一个例子：
假如当前length=16,其length-1的二进制表示为：
00000000 00000000 00000000 00001111
若当前hash值为形如这样的二进制：
******** ******** ******** ****0000
（注：*可为0也可为1），此两数的与（&）运算永远相等，等于0，
那这样的结果真是太糟糕了，很明显不是一个好的散列算法。

但是如果我们将 hashCode 值右移 16 位，也就是取 int 类型的一半，刚好将该二进制数对半切开。
并且使用位异或运算（如果两个数对应的位置相反，则结果为1，反之为0），这样的话，就能避免我们上面的情况的发生。
总的来说，使用位移 16 位和 异或 就是防止这种极端情况。但是，该方法在一些极端情况下还是有问题，比如：10000000000000000000000000 和 
1000000000100000000000000 这两个数，如果数组长度是16，那么即使右移16位，在异或，hash 值还是会重复。但是为了性能，
对这种极端情况，JDK 的作者选择了性能。毕竟这是少数情况，为了这种情况去增加 hash 时间，性价比不高。
```

**2、为什么使用 & 与运算代替模运算？**

```shell
对于计算机来说，除法和求余数（模运算）是最慢的动作，而与操作要快很多。
我们再来看一个数学等式：a % b == (b-1) & a ,当b是2的指数时，等式成立。
我们看个例子：
假设length为16，那么n-1的二进制为1111，
1111 & ******** ******** ******** ****xxxx 结果为xxxx
可以看到，当 n 为 2 的幂次方的时候，减一之后就会得到 1111* 的数字，这个数字正好可以掩码。并且得到的结果取决于 hash 值。
因为 hash 值是1，那么最终的结果也是1 ，hash 值是0，最终的结果也是0。
```

**3、HashMap怎么保证数组的容量为2的幂次方的？**
我们来看源码：

```java
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;//取高位的1个1
        n |= n >>> 2;//取高位的2个1
        n |= n >>> 4;//取高位的4个1
        n |= n >>> 8;//取高位的8个1
        n |= n >>> 16;//取高位的16个1
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }

我们来举个例子看一下：
10000000 00000000 00000000 00000000
n >>> 1操作后为：
01000000 00000000 00000000 00000000
n |= n >>> 1操作后为：
11000000 00000000 00000000 00000000
 n |= n >>> 2操作后为：
11110000 00000000 00000000 00000000
n |= n >>> 4操作后为：
11111111 00000000 00000000 00000000
n |= n >>> 8操作后为：
11111111 11111111 00000000 00000000
n |= n >>> 16操作后为：
11111111 11111111 11111111 11111111
+1操作后为：
1 00000000 00000000 00000000 00000000
所以保证了容量为2的次幂。
```

**put方法**

1. 判断键值对数组table[i]是否为空或为null，否则执行resize()进行扩容。
2. 根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向步骤6，如果table[i]不为空，转向步骤3。
3. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向步骤4，这里的相同指的是hashCode以及equals。
4. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向步骤5。
5. 遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可。
6. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容。

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        // 步骤1：tab为空则创建
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 步骤2：计算index，并对null做处理
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            // 步骤3：节点key存在，直接覆盖value
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            // 步骤4：判断该链为红黑树
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            // 步骤5：该链为链表
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //链表长度大于8转换为红黑树进行处理
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                     // key已经存在直接覆盖value
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        // 步骤6：超过最大容量 就扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

**扩容机制**

扩容(resize)就是重新计算容量，向HashMap对象里不停的添加元素，而HashMap对象内部的数组无法装载更多的元素时，对象就需要扩大数组的长度，以便能装入更多的元素。当然Java里的数组是无法自动扩容的，方法是使用一个新的数组代替已有的容量小的数组，就像我们用一个小桶装水，如果想装更多的水，就得换大水桶。

我们分析下resize的源码，鉴于JDK1.8融入了红黑树，较复杂，为了便于理解我们仍然使用JDK1.7的代码，好理解一些。

```java
 void resize(int newCapacity) {   //传入新的容量
     Entry[] oldTable = table;    //引用扩容前的Entry数组
     int oldCapacity = oldTable.length;         
     if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
         threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
         return;
     }
  
     Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
     transfer(newTable);                         //！！将数据转移到新的Entry数组里
     table = newTable;                           //HashMap的table属性引用新的Entry数组
     threshold = (int)(newCapacity * loadFactor);//修改阈值
 }
```

这里就是使用一个容量更大的数组来代替已有的容量小的数组，transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里。

```java
 void transfer(Entry[] newTable) {
     Entry[] src = table;                   //src引用了旧的Entry数组
     int newCapacity = newTable.length;
     for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
         Entry<K,V> e = src[j];             //取得旧Entry数组的每个元素
         if (e != null) {
             src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
             do {
                 Entry<K,V> next = e.next;
                 int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
                 e.next = newTable[i]; //标记[1]
                 newTable[i] = e;      //将元素放在数组上
                 e = next;             //访问下一个Entry链上的元素
             } while (e != null);
         }
     }
 } 
```

newTable[i]的引用赋给了e.next，也就是使用了单链表的头插入方式，同一位置上新元素总会被放在链表的头部位置；这样先放在一个索引上的元素终会被放到Entry链的尾部(如果发生了hash冲突的话），这一点和Jdk1.8有区别，下文详解。在旧数组中同一条Entry链上的元素，通过重新计算索引位置后，有可能被放到了新数组的不同位置上。

下面举个例子说明下扩容过程。假设了我们的hash算法就是简单的用key mod 一下表的大小（也就是数组的长度）。其中的哈希桶数组table的size=2， 所以key = 3、7、5，put顺序依次为 5、7、3。在mod 2以后都冲突在table[1]这里了。这里假设负载因子 loadFactor=1，即当键值对的实际大小size 大于 table的实际大小时进行扩容。接下来的三个步骤是哈希桶数组 resize成4，然后所有的Node重新rehash的过程。

![20191016203009515](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191016203009515.png)

下面我们讲解下JDK1.8做了哪些优化。经过观测可以发现，我们使用的是2次幂的扩展(指长度扩为原来2倍)，所以，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置。看下图可以明白这句话的意思，n为table的长度，图（a）表示扩容前的key1和key2两种key确定索引位置的示例，图（b）表示扩容后key1和key2两种key确定索引位置的示例，其中hash1是key1对应的哈希与高位运算结果。

![20191016203105131](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191016203105131.png)

元素在重新计算hash之后，因为n变为2倍，那么n-1的mask范围在高位多1bit(红色)，因此新的index就会发生这样的变化：

![20191016203157880](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191016203157880.png)

因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap”，可以看看下图为16扩充为32的resize示意图：

![20191016203232251](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191016203232251.png)

这个设计确实非常的巧妙，既省去了重新计算hash值的时间，而且同时，由于新增的1bit是0还是1可以认为是随机的，因此resize的过程，均匀的把之前的冲突的节点分散到新的bucket了。这一块就是JDK1.8新增的优化点。有一点注意区别，JDK1.7中rehash的时候，旧链表迁移新链表的时候，如果在新表的数组索引位置相同，则链表元素会倒置，但是从上图可以看出，JDK1.8不会倒置。

JDK1.8的resize源码，如下:

```java
 final Node<K,V>[] resize() {
     Node<K,V>[] oldTab = table;
     int oldCap = (oldTab == null) ? 0 : oldTab.length;
     int oldThr = threshold;
     int newCap, newThr = 0;
     if (oldCap > 0) {
         // 超过最大值就不再扩充了，就只好随你碰撞去吧
         if (oldCap >= MAXIMUM_CAPACITY) {
             threshold = Integer.MAX_VALUE;
             return oldTab;
         }
         // 没超过最大值，就扩充为原来的2倍
         else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                  oldCap >= DEFAULT_INITIAL_CAPACITY)
             newThr = oldThr << 1; // double threshold
     }
     else if (oldThr > 0) // initial capacity was placed in threshold
         newCap = oldThr;
     else {               // zero initial threshold signifies using defaults
         newCap = DEFAULT_INITIAL_CAPACITY;
         newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
     }
     // 计算新的resize上限
     if (newThr == 0) {
 
         float ft = (float)newCap * loadFactor;
         newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                   (int)ft : Integer.MAX_VALUE);
     }
     threshold = newThr;
     @SuppressWarnings({"rawtypes"，"unchecked"})
         Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
     table = newTab;
     if (oldTab != null) {
         // 把每个bucket都移动到新的buckets中
         for (int j = 0; j < oldCap; ++j) {
             Node<K,V> e;
             if ((e = oldTab[j]) != null) {
                 oldTab[j] = null;
                 if (e.next == null)
                     newTab[e.hash & (newCap - 1)] = e;
                 else if (e instanceof TreeNode)
                     ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                 else { // 链表优化重hash的代码块
                     Node<K,V> loHead = null, loTail = null;
                     Node<K,V> hiHead = null, hiTail = null;
                     Node<K,V> next;
                     do {
                         next = e.next;
                         // 原索引
                         if ((e.hash & oldCap) == 0) {
                             if (loTail == null)
                                 loHead = e;
                             else
                                 loTail.next = e;
                             loTail = e;
                         }
                         // 原索引+oldCap
                         else {
                             if (hiTail == null)
                                 hiHead = e;
                             else
                                 hiTail.next = e;
                             hiTail = e;
                         }
                     } while ((e = next) != null);
                     // 原索引放到bucket里
                     if (loTail != null) {
                         loTail.next = null;
                         newTab[j] = loHead;
                     }
                     // 原索引+oldCap放到bucket里
                     if (hiTail != null) {
                         hiTail.next = null;
                         newTab[j + oldCap] = hiHead;
                     }
                 }
             }
         }
     }
     return newTab;
 }
```

注：put方法和扩容机制参考https://tech.meituan.com/2016/06/24/java-hashmap.html，原作者分析的太好了，直接拿来用了，感谢！

**为什么1.8之后超过8会转化为红黑树**

为什么不是平衡二叉树？

**AVL树(平衡二叉树)**
AVL树是带有平衡条件的二叉查找树，一般是用平衡因子差值判断是否平衡并通过旋转来实现平衡，左右子树高度差不超过1，和红黑树相比，AVL树是严格的平衡二叉树，平衡条件必须满足(所有结点的左右子树高度差不超过1)。不管我们是执行插入还是删除操作，只要不满足上面的条件，就要通过旋转来保存平衡，而因为旋转非常耗时，由此我们可以知道AVL树适合用于插入与删除次数比较少，但查找多的情况。

**局限性：**
由于维护这种高度平衡所付出的代价比从中获得的效率收益还大，故而实际的应用不多，更多的地方是用追求局部而不是非常严格整体平衡的红黑树。当然，如果应用场景中对插入删除不频繁，只是对查找要求较高，那么AVL还是较优于红黑树。

**红黑树**
一种二叉查找树，但在每个节点增加一个存储位表示结点的颜色，可以是红或黑(非红即黑)。通过对任何一条从根到叶子的路径上各个节点着色的方式的限制，红黑树确保没有一条路径会比其他路径长出两倍，因此，红黑树是一中弱平衡二叉树(由于是弱平衡，可以看到，在相同的节点情况下，AVL树的高度低于红黑树)，相对于要求严格的AVL树来说，它的旋转次数少，插入最多两次旋转，删除最多三次旋转，所以对于搜索，插入，删除操作较多的情况下，我们就用红黑树。

**特点：**

1. 结点非红即黑
2. 根结点是黑色的
3. 每个叶子节点(NULL节点)是黑色的
4. 每个红色节点的两个子节点都是黑色的。(不能有两连续的红色节点)
5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。



## HashTable

- HashTable和HashMap实现大致相同，都是基于哈希表来实现的，数组+链表的形式（和HashMap有稍微的区别，HashMap加入了红黑树），它存储的内容是键值对(key-value)映射。
- Hashtable 继承于Dictionary，实现了Map、Cloneable、java.io.Serializable接口。Dictionary是一个过时的键值对映射的抽象类，jdk已经不建议使用，新的实现应该实现Map接口，而不是扩展这个类。
- HashTable方法加了synchronized关键字，所以是线程安全的。

**重要字段**

```java
private transient Entry<?,?>[] table;
private transient int count;
private int threshold;
private float loadFactor;
private transient int modCount = 0;
```

table实现哈希表的数组结构，数组长度最小为1，默认为11，里面存储着Entry

```java
private static class Entry<K,V> implements Map.Entry<K,V> {
        final int hash;//哈希值，用于定位数组索引位置
        final K key;//键
        V value;//值
        Entry<K,V> next;//链表下一个元素
	}
```

Entry是HashTable的一个内部类，实现了Map.Entry接口，本质是就是一个映射(键值对)。

count是HashTable中实际存在的键值对数量，而modCount字段主要用来记录HashTable内部结构发生变化的次数，主要用于迭代的快速失败。强调一点，内部结构发生变化指的是结构发生变化，例如put新键值对，但是某个key对应的value值被覆盖不属于结构变化。

loadFactor为负载因子(默认值是0.75)，threshold是HashTable所能容纳的最大数据量的Entry(键值对)个数。threshold = length * loadFactor。也就是说，在数组定义好长度之后，负载因子越大，所能容纳的键值对个数越多。

threshold就是在此loadFactor和length(数组长度)对应下允许的最大元素数目，超过这个数目就重新rehash(扩容)，扩容后的HashTable容量是之前容量的两倍+1。默认的负载因子0.75是对空间和时间效率的一个平衡选择，建议不要修改，除非在时间和空间比较特殊的情况下，如果内存空间很多而又对时间效率要求很高，可以降低负载因子loadFactor的值；相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子loadFactor的值，这个值可以大于1。

**扩容机制**

当前键值对的数量count >= threshold时会触发扩容。

```java
protected void rehash() {
        int oldCapacity = table.length;
        Entry<?,?>[] oldMap = table;

        //新容量=旧容量 * 2 + 1
        int newCapacity = (oldCapacity << 1) + 1;
        if (newCapacity - MAX_ARRAY_SIZE > 0) {
            if (oldCapacity == MAX_ARRAY_SIZE)
                // Keep running with MAX_ARRAY_SIZE buckets
                return;
            newCapacity = MAX_ARRAY_SIZE;
        }
        //新建一个size = newCapacity的数组
        Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];

        modCount++;
        //重新计算阀值
        threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
        table = newMap;
		//将原来的元素拷贝到新的HashTable中，对数组链表数据进行重新 hash index 计算，
		//rehash之后会使得最早插入的数据回到链表的第一位
        for (int i = oldCapacity ; i-- > 0 ;) {
            for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null ; ) {
                Entry<K,V> e = old;
                old = old.next;

                int index = (e.hash & 0x7FFFFFFF) % newCapacity;
                e.next = (Entry<K,V>)newMap[index];
                newMap[index] = e;
            }
        }
    }
```

在这个rehash()方法中我们可以看到容量扩大两倍+1，同时需要将原来HashTable中的元素一一复制到新的HashTable中,并且对每个元素根据hash值从新计算下标，这个过程是比较消耗时间的。

**put方法**

put方法的整个处理流程：计算key的hash值，根据hash值获得key在table数组中的索引位置，然后迭代该key处的Entry链表（我们暂且理解为链表），若该链表中存在一个这个的key对象，那么就直接替换其value值即可，否则在将改key-value节点插入该index索引位置处。

```java
public synchronized V put(K key, V value) {
        // 值不能为空
        if (value == null) {
            throw new NullPointerException();
        }

        //计算key的hash值，确认在table[]中的索引位置
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        Entry<K,V> entry = (Entry<K,V>)tab[index];
        //迭代index索引位置，如果该位置处的链表中存在一个一样的key，则替换其value，返回旧值
        for(; entry != null ; entry = entry.next) {
            if ((entry.hash == hash) && entry.key.equals(key)) {
                V old = entry.value;
                entry.value = value;
                return old;
            }
        }
		//如果不存在，则创建entry加入hash表中
        addEntry(hash, key, value, index);
        return null;
    }
//在指定索引位置加入key-value键值对
private void addEntry(int hash, K key, V value, int index) {
        modCount++;

        Entry<?,?> tab[] = table;
        //如果当前元素的数量大于等于阈值，则触发扩容
        if (count >= threshold) {
            // Rehash the table if the threshold is exceeded
            rehash();

            tab = table;
            hash = key.hashCode();
            index = (hash & 0x7FFFFFFF) % tab.length;
        }

        // 创建entry并加入到链表的头
        @SuppressWarnings("unchecked")
        Entry<K,V> e = (Entry<K,V>) tab[index];
        tab[index] = new Entry<>(hash, key, value, e);
        count++;
    }
```

**get方法**

get方法比较简单，处理过程就是计算key的hash值，判断在table数组中的索引位置，然后迭代链表，匹配直到找到相对应key的value,若没有找到返回null。

```java
public synchronized V get(Object key) {
        Entry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                return (V)e.value;
            }
        }
        return null;
    }
```

**HashTable和HashMap的区别**

- HashMap线程不安全，HashTable是线程安全的。HashMap内部实现没有任何线程同步相关的代码，所以相对而言性能要好一点。如果在多线程中使用HashMap需要自己管理线程同步。HashTable大部分对外接口都使用synchronized包裹，所以是线程安全的，但是性能会相对差一些。
- 二者的基类不一样。HashMap派生于AbstractMap，HashTable派生于Dictionary。它们都实现Map, Cloneable, Serializable这些接口。AbstractMap中提供的基础方法更多，并且实现了多个通用的方法，而在Dictionary中只有少量的接口，并且都是abstract类型。
- key和value的取值范围不同。HashMap的key和value都可以为null，但是HashTable key和value都不能为null。对于HashMap如果get返回null，并不能表明HashMap不存在这个key，如果需要判断HashMap中是否包含某个key，就需要使用containsKey这个方法来判断。
- 算法不一样。HashMap的initialCapacity为16，而HashTable的initialCapacity为11。HashMap中初始容量必须是2的幂,如果初始化传入的initialCapacity不是2的幂，将会自动调整为大于出入的initialCapacity最小的2的幂。HashMap使用自己的计算hash的方法(会依赖key的hashCode方法)，HashTable则使用key的hashCode方法得到。



## LinkedHashMap

**概要**

HashMap是Java集合中的重要成员，也是Map族中我们最为常用的一种，但是HashMap是无序的，也就是说，迭代HashMap所得到的元素顺序并不是它们最初放置到HashMap的顺序。

HashMap的这一缺点往往会造成诸多不便，因为在有些场景中，我们确需要用到一个可以保持插入顺序的Map。庆幸的是，JDK为我们解决了这个问题，它为HashMap提供了一个子类 —— LinkedHashMap。虽然LinkedHashMap增加了时间和空间上的开销，但是它通过维护一个额外的双向链表保证了迭代顺序。

该迭代顺序可以是插入顺序，也可以是访问顺序。因此，根据链表中元素的顺序可以将LinkedHashMap分为：保持插入顺序的LinkedHashMap和保持访问顺序的LinkedHashMap，其中LinkedHashMap的默认实现是按插入顺序排序的。

其实LinkedHashMap就是 HashMap+双向链表 来实现的，就是将所有Node节点链入一个双向链表的HashMap。在LinkedHashMapMap中，所有put进来的Node都保存在哈希表中，但由于它又额外定义了一个以head为头结点的双向链表，因此对于每次put进来Node，除了将其保存到哈希表中对应的位置上之外，还会将其插入到双向链表的尾部。

![20191020123633211](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/20191020123633211.png)

**源码分析**

**类信息**
LinkedHashMap继承于HashMap，并在此基础上扩展了双向链表。

```java
public class LinkedHashMap<K,V> extends HashMap<K,V>
    implements Map<K,V>
```

**成员变量**

与HashMap相比，LinkedHashMap增加了两个属性用于保证迭代顺序，分别是 双向链表头结点head尾结点tail 和 标志位accessOrder (值为true时，表示按照访问顺序迭代；值为false时，表示按照插入顺序迭代)。

```java
	//头结点
    transient LinkedHashMap.Entry<K,V> head;
	//尾结点
    transient LinkedHashMap.Entry<K,V> tail;
    //true表示按照访问顺序迭代，false时表示按照插入顺序
    final boolean accessOrder;
```

元素类
LinkedHashMap采用的hash算法和HashMap相同，但是它重新定义了Entry。LinkedHashMap中的Entry增加了两个指针 before 和 after，它们分别用于维护双向链接列表。特别需要注意的是，next用于维护HashMap各个桶中Entry的连接顺序，before、after用于维护Entry插入的先后顺序的，源代码如下：

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }
```

**构造函数**
LinkedHashMap 一共提供了五个构造函数，它们都是在HashMap的构造函数的基础上实现的，除了默认空参数构造方法，下面这个构造函数包含了大部分其他构造方法使用的参数。

```java
public LinkedHashMap(int initialCapacity,
                         float loadFactor,
                         boolean accessOrder) {
        super(initialCapacity, loadFactor);// 调用HashMap对应的构造函数
        this.accessOrder = accessOrder;//指定迭代顺序方式
    }
```

**put方法**
LinkedHashMap没有对 put(key,vlaue) 方法进行任何直接的修改，完全继承了HashMap的 put(Key,Value) 方法，HashMap中put源码如下：

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //tab为空则创建
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //计算index，并对null做处理
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            //节点key存在，直接覆盖value
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //判断该链为红黑树
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //该链为链表
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //链表长度大于8转换为红黑树进行处理
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                     // key已经存在直接覆盖value
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        //超过最大容量 就扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

在LinkedHashMap中，它对newNode方法进行了重写。下面我们对比地看一下LinkedHashMap 和HashMap的newNode方法的具体实现：

```java
//LinkedHashMap
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
        LinkedHashMap.Entry<K,V> p =
            new LinkedHashMap.Entry<K,V>(hash, key, value, e);
        linkNodeLast(p);
        return p;
    }
private void linkNodeLast(LinkedHashMap.Entry<K,V> p) {
        LinkedHashMap.Entry<K,V> last = tail;
        tail = p;
        if (last == null)
            head = p;
        else {
            p.before = last;
            last.after = p;
        }
    }

//HashMap
​```java
Node<K,V> newNode(int hash, K key, V value, Node<K,V> next) {
        return new Node<>(hash, key, value, next);
    }
```

可见在LinkedHashMap中向哈希表中插入新Node的同时，还会通过linkNodeLast方法将其链入到双向链表中，相比HashMap而言，LinkedHashMap在向哈希表添加一个键值对的同时，也会将其链入到它所维护的双向链表中，以便设定迭代顺序。

**get方法**
相对于LinkedHashMap的存储而言，读取就显得比较简单了。LinkedHashMap中重写了HashMap中的get方法，源码如下：

```java
public V get(Object key) {
        Node<K,V> e;
        if ((e = getNode(hash(key), key)) == null)
            return null;
        if (accessOrder)
            afterNodeAccess(e);
        return e.value;
    }
```

在LinkedHashMap的get方法中，通过HashMap中的getNode方法获取Node对象。注意这里的afterNodeAccess方法，如果链表中元素的排序规则是按照插入的先后顺序排序的话（当accessOrder为false时），该方法什么也不做；如果链表中元素的排序规则是按照访问的先后顺序排序的话（当accessOrder为true时），则将e移到链表的末尾处。

```java
void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        // 判断迭代策略，并且当前节点不是尾节点
        if (accessOrder && (last = tail) != e) {
        	// 记录当前节点，并获取前后节点
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            // 把当前节点的 after 节点置 null
            p.after = null;
            // 如果当前节点是头节点，把后一个节点置为头节点
            if (b == null)
                head = a;
            // 把当前节点的前后节点相连
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            // 把当前节点置为尾节点并记录
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }
```

**扩容机制**

由于LinkedHashMap并没有重写HashMap的扩容，所以其扩容是和HashMap保持一致的，具体可以参见[吃透Java集合系列九：HashMap](https://blog.csdn.net/u013277209/article/details/102551739)



## TreeMap

**TreeMap整体认识**

我们知道HashMap，它保证了以O(1)的时间复杂度进行增、删、改、查，从存储角度考虑，这两种数据结构是非常优秀的。但是HashMap还是有自己的局限性----**它不具备统计性能，或者说它的统计性能时间复杂度并不是很好才更准确，所有的统计必须遍历所有Entry，因此时间复杂度为O(N)。**
比如Map的Key有1、2、3、4、5、6、7，我现在要统计：

1. 所有Key比3大的键值对有哪些
2. Key最小的和Key最大的是哪两个

就类似这些操作，HashMap做得比较差，此时我们可以使用TreeMap。TreeMap的Key按照自然顺序进行排序或者根据创建映射时提供的Comparator接口进行排序。**TreeMap为增、删、改、查这些操作提供了log(N)的时间开销，从存储角度而言，这比HashMap的O(1)时间复杂度要差些；但是在统计性能上，TreeMap同样可以保证log(N)的时间开销，这又比HashMap的O(N)时间复杂度好不少。**

**因此：如果只需要存储功能，使用HashMap是一种更好的选择；如果还需要保证统计性能或者需要对Key按照一定规则进行排序，那么使用TreeMap是一种更好的选择。**

TreeMap是由红黑树来实现的，下面看一下红黑树。

**红黑树**

红黑树又称红-黑二叉树，它首先是一颗二叉树，它具体二叉树所有的特性。同时红黑树更是一颗自平衡的排序二叉树。

我们知道一颗基本的二叉树他们都需要满足一个基本性质–即树中的任何节点的值大于它的左子节点，且小于它的右子节点。按照这个基本性质使得树的检索效率大大提高。我们知道在生成二叉树的过程是非常容易失衡的，最坏的情况就是一边倒（只有右/左子树），这样势必会导致二叉树的检索效率大大降低（O(n)），所以为了维持二叉树的平衡，大牛们提出了各种实现的算法，如：AVL，SBT，伸展树，TREAP ，红黑树等等。

平衡二叉树必须具备如下特性：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。也就是说该二叉树的任何一个等等子节点，其左右子树的高度都相近。

红黑树顾名思义就是节点是红色或者黑色的平衡二叉树，它通过颜色的约束来维持着二叉树的平衡。对于一棵有效的红黑树二叉树而言我们必须增加如下规则：

1. 每个节点都只能是红色或者黑色
2. 根节点是黑色
3. 每个叶节点（NIL节点，空节点）是黑色的。
4. 如果一个结点是红的，则它两个子节点都是黑的。也就是说在一条路径上不能出现相邻的两个红色结点。
5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。

这些约束强制了红黑树的关键性质: 从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这棵树大致上是平衡的。因为操作比如插入、删除和查找某个值的最坏情况时间都要求与树的高度成比例，这个在高度上的理论上限允许红黑树在最坏情况下都是高效的，而不同于普通的二叉查找树。所以红黑树它是复杂而高效的，其检索效率O(log n)。下图为一颗典型的红黑二叉树。

红黑树不是重点，具体了解红黑树可以参考：
[红黑树系列集锦](https://blog.csdn.net/v_JULY_v/article/category/774945)
[红黑树数据结构剖析](https://www.cnblogs.com/fanzhidongyzby/p/3187912.html)

**源码分析**

**类信息**

```java
public class TreeMap<K,V>
    extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, java.io.Serializable
```

TreeMap继承AbstractMap，实现NavigableMap、Cloneable、Serializable三个接口。其中AbstractMap表明TreeMap为一个Map即支持key-value的集合， NavigableMap则意味着它支持一系列的导航方法，具备针对给定搜索目标返回最接近匹配项的导航方法 。

**属性信息**

```java
	//比较器，因为TreeMap是有序的，通过comparator接口我们可以对TreeMap的内部排序进行精密的控制
	//可以通过构造函数自定义比较器，也可以使用默认比较器
	private final Comparator<? super K> comparator;
	//treeMap的根节点
    private transient Entry<K,V> root = null;
	//容器大小
    private transient int size = 0;
	//修改次数
    private transient int modCount = 0;
```

节点的Entry内部类信息

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
		//键
        K key;
        //值
        V value;
        //左孩子
        Entry<K,V> left = null;
        //右孩子
        Entry<K,V> right = null;
        //父节点
        Entry<K,V> parent;
        //颜色
        boolean color = BLACK;
```

**put方法**

put方法步骤如下：

1. 获取根节点，根节点为空，产生一个根节点，将其着色为黑色，退出余下流程
2. 获取比较器，如果传入的Comparator接口不为空，使用传入的Comparator接口实现类进行比较；如果传入的Comparator接口为空，将Key强转为Comparable接口进行比较
3. 从根节点开始逐一依照规定的排序算法进行比较，取比较值cmp，如果cmp=0，表示插入的Key已存在；如果cmp>0，取当前节点的右子节点；如果cmp<0，取当前节点的左子节点
4. 排除插入的Key已存在的情况，第（3）步的比较一直比较到当前节点t的左子节点或右子节点为null，此时t就是我们寻找到的节点，cmp>0则准备往t的右子节点插入新节点，cmp<0则准备往t的左子节点插入新节点
5. new出一个新节点，默认为黑色，根据cmp的值向t的左边或者右边进行插入
6. 插入之后进行修复，包括左旋、右旋、重新着色这些操作，让树保持平衡性

```java
public V put(K key, V value) {
           //用t表示二叉树的当前节点
            Entry<K,V> t = root;
            //t为null表示一个空树，即TreeMap中没有任何元素，直接插入
            if (t == null) {
                //比较key值，个人觉得这句代码没有任何意义，空树还需要比较、排序？
                compare(key, key); // type (and possibly null) check
                //将新的key-value键值对创建为一个Entry节点，并将该节点赋予给root
                root = new Entry<>(key, value, null);
                //容器的size = 1，表示TreeMap集合中存在一个元素
                size = 1;
                //修改次数 + 1
                modCount++;
                return null;
            }
            int cmp;     //cmp表示key排序的返回结果
            Entry<K,V> parent;   //父节点
            // split comparator and comparable paths
            Comparator<? super K> cpr = comparator;    //指定的排序算法
            //如果cpr不为空，则采用既定的排序算法进行创建TreeMap集合
            if (cpr != null) {
                do {
                    parent = t;      //parent指向上次循环后的t
                    //比较新增节点的key和当前节点key的大小
                    cmp = cpr.compare(key, t.key);
                    //cmp返回值小于0，表示新增节点的key小于当前节点的key，则以当前节点的左子节点作为新的当前节点
                    if (cmp < 0)
                        t = t.left;
                    //cmp返回值大于0，表示新增节点的key大于当前节点的key，则以当前节点的右子节点作为新的当前节点
                    else if (cmp > 0)
                        t = t.right;
                    //cmp返回值等于0，表示两个key值相等，则新值覆盖旧值，并返回新值
                    else
                        return t.setValue(value);
                } while (t != null);
            }
            //如果cpr为空，则采用默认的排序算法进行创建TreeMap集合
            else {
                if (key == null)     //key值为空抛出异常
                    throw new NullPointerException();
                /* 下面处理过程和上面一样 */
                Comparable<? super K> k = (Comparable<? super K>) key;
                do {
                    parent = t;
                    cmp = k.compareTo(t.key);
                    if (cmp < 0)
                        t = t.left;
                    else if (cmp > 0)
                        t = t.right;
                    else
                        return t.setValue(value);
                } while (t != null);
            }
            //将新增节点当做parent的子节点
            Entry<K,V> e = new Entry<>(key, value, parent);
            //如果新增节点的key小于parent的key，则当做左子节点
            if (cmp < 0)
                parent.left = e;
          //如果新增节点的key大于parent的key，则当做右子节点
            else
                parent.right = e;
            /*
             *  上面已经完成了排序二叉树的的构建，将新增节点插入该树中的合适位置
             *  下面fixAfterInsertion()方法就是对这棵树进行调整、平衡，具体过程参考上面的五种情况
             */
            fixAfterInsertion(e);
            //TreeMap元素数量 + 1
            size++;
            //TreeMap容器修改次数 + 1
            modCount++;
            return null;
        }
```

第1~第5步都没有什么问题，红黑树最核心的应当是第6步插入数据之后进行的修复工作，对应的Java代码是TreeMap中的fixAfterInsertion方法

```java
	/**
     * 新增节点后的修复操作
     * x 表示新增节点
     */
     private void fixAfterInsertion(Entry<K,V> x) {
            x.color = RED;    //新增节点的颜色为红色

            //循环 直到 x不是根节点，且x的父节点不为红色
            while (x != null && x != root && x.parent.color == RED) {
                //如果X的父节点（P）是其父节点的父节点（G）的左节点
                if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
                    //获取X的叔节点(U)
                    Entry<K,V> y = rightOf(parentOf(parentOf(x)));
                    //如果X的叔节点（U） 为红色（情况三）
                    if (colorOf(y) == RED) {     
                        //将X的父节点（P）设置为黑色
                        setColor(parentOf(x), BLACK);
                        //将X的叔节点（U）设置为黑色
                        setColor(y, BLACK);
                        //将X的父节点的父节点（G）设置红色
                        setColor(parentOf(parentOf(x)), RED);
                        x = parentOf(parentOf(x));
                    }
                    //如果X的叔节点（U为黑色）；这里会存在两种情况（情况四、情况五）
                    else {   
                        //如果X节点为其父节点（P）的右子树，则进行左旋转（情况四）
                        if (x == rightOf(parentOf(x))) {
                            //将X的父节点作为X
                            x = parentOf(x);
                            //右旋转
                            rotateLeft(x);
                        }
                        //（情况五）
                        //将X的父节点（P）设置为黑色
                        setColor(parentOf(x), BLACK);
                        //将X的父节点的父节点（G）设置红色
                        setColor(parentOf(parentOf(x)), RED);
                        //以X的父节点的父节点（G）为中心右旋转
                        rotateRight(parentOf(parentOf(x)));
                    }
                }
                //如果X的父节点（P）是其父节点的父节点（G）的右节点
                else {
                    //获取X的叔节点（U）
                    Entry<K,V> y = leftOf(parentOf(parentOf(x)));
                  //如果X的叔节点（U） 为红色（情况三）
                    if (colorOf(y) == RED) {
                        //将X的父节点（P）设置为黑色
                        setColor(parentOf(x), BLACK);
                        //将X的叔节点（U）设置为黑色
                        setColor(y, BLACK);
                        //将X的父节点的父节点（G）设置红色
                        setColor(parentOf(parentOf(x)), RED);
                        x = parentOf(parentOf(x));
                    }
                  //如果X的叔节点（U为黑色）；这里会存在两种情况（情况四、情况五）
                    else {
                        //如果X节点为其父节点（P）的右子树，则进行左旋转（情况四）
                        if (x == leftOf(parentOf(x))) {
                            //将X的父节点作为X
                            x = parentOf(x);
                           //右旋转
                            rotateRight(x);
                        }
                        //（情况五）
                        //将X的父节点（P）设置为黑色
                        setColor(parentOf(x), BLACK);
                        //将X的父节点的父节点（G）设置红色
                        setColor(parentOf(parentOf(x)), RED);
                        //以X的父节点的父节点（G）为中心右旋转
                        rotateLeft(parentOf(parentOf(x)));
                    }
                }
            }
            //将根节点G强制设置为黑色
            root.color = BLACK;
        }
```

**get方法**

get方法相当于put方法就简单多了，从根节点开始循环比较。

- 当key大于当前节点，把当前节点指针指向左孩子继续循环。
- 当key小于当前节点，把当前节点的指针指向右孩子继续循环。
- 当key等于当前节点，则返回当前节点。

```java
public V get(Object key) {
		// 根据 key 获取到对应的 Entry
        Entry<K,V> p = getEntry(key);
        return (p==null ? null : p.value);
    }
final Entry<K,V> getEntry(Object key) {
        // 基于比较器的查找
        if (comparator != null)
            return getEntryUsingComparator(key);
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
            Comparable<? super K> k = (Comparable<? super K>) key;
        //从根节点开始查找，当key小于当前节点，左孩子为当前节点继续循环，当key大于当前节点
        //右孩子为当前节点继续循环，key等于当前节点，则返回当前节点
        Entry<K,V> p = root;
        while (p != null) {
            int cmp = k.compareTo(p.key);
            if (cmp < 0)
                p = p.left;
            else if (cmp > 0)
                p = p.right;
            else
                return p;
        }
        return null;
    }
```

**remove方法**

针对于红黑树的增加节点而言，删除显得更加复杂，使原本就复杂的红黑树变得更加复杂。同时删除节点和增加节点一样，同样是找到删除的节点，删除之后调整红黑树。但是这里的删除节点并不是直接删除，而是通过走了“弯路”通过一种捷径来删除的：找到被删除的节点D的子节点C，用C来替代D，不是直接删除D，因为D被C替代了，直接删除C即可。所以这里就将删除父节点D的事情转变为了删除子节点C的事情，这样处理就将复杂的删除事件简单化了。子节点C的规则是：右分支最左边，或者 左分支最右边的。

红-黑二叉树删除节点，最大的麻烦是要保持 各分支黑色节点数目相等。 因为是删除，所以不用担心存在颜色冲突问题——插入才会引起颜色冲突。

红黑树删除节点同样会分成几种情况，这里是按照待删除节点有几个儿子的情况来进行分类：

1. 没有儿子，即为叶结点。直接把父结点的对应儿子指针设为NULL，删除儿子结点就OK了。
2. 只有一个儿子。那么把父结点的相应儿子指针指向儿子的独生子，删除儿子结点也OK了。
3. 有两个儿子。这种情况比较复杂，但还是比较简单。上面提到过用子节点C替代代替待删除节点D，然后删除子节点C即可。

```java
public V remove(Object key) {
        Entry<K,V> p = getEntry(key);
        if (p == null)
            return null;

        V oldValue = p.value;
        deleteEntry(p);
        return oldValue;
    }
private void deleteEntry(Entry<K,V> p) {
        modCount++;
        size--;

        // If strictly internal, copy successor's element to p and then make p
        // point to successor.
        if (p.left != null && p.right != null) {
            Entry<K,V> s = successor(p);
            p.key = s.key;
            p.value = s.value;
            p = s;
        } // p has 2 children

        // Start fixup at replacement node, if it exists.
        Entry<K,V> replacement = (p.left != null ? p.left : p.right);

        if (replacement != null) {
            // Link replacement to parent
            replacement.parent = p.parent;
            if (p.parent == null)
                root = replacement;
            else if (p == p.parent.left)
                p.parent.left  = replacement;
            else
                p.parent.right = replacement;

            // Null out links so they are OK to use by fixAfterDeletion.
            p.left = p.right = p.parent = null;

            // Fix replacement
            if (p.color == BLACK)
                fixAfterDeletion(replacement);
        } else if (p.parent == null) { // return if we are the only node.
            root = null;
        } else { //  No children. Use self as phantom replacement and unlink.
            if (p.color == BLACK)
                fixAfterDeletion(p);

            if (p.parent != null) {
                if (p == p.parent.left)
                    p.parent.left = null;
                else if (p == p.parent.right)
                    p.parent.right = null;
                p.parent = null;
            }
        }
    }
```

删除后的调整

删除元素之后的调整和前面的插入元素调整的过程比起来更复杂。它不是一个简单的在原来过程中取反。我们先从一个最基本的点开始入手。首先一个，我们要进行调整的这个点肯定是因为我们要删除的这个点破坏了红黑树的本质特性。而如果我们删除的这个点是红色的，则它肯定不会破坏里面的属性。因为从前面删除的过程来看，我们这个要删除的点是已经在濒临叶节点的附近了，它要么有一个子节点，要么就是一个叶节点。如果它是红色的，删除了，从上面的节点到叶节点所经历的黑色节点没有变化。所以，这里的一个前置条件就是待删除的节点是黑色的。

在前面的那个前提下，我们要调整红黑树的目的就是要保证，这个原来是黑色的节点被删除后，我们要通过一定的变化，使得他们仍然是合法的红黑树。我们都知道，在一个黑色节点被删除后，从上面的节点到它所在的叶节点路径所经历的黑色节点就少了一个。我们需要做一些调整，使得它少的这个在后面某个地方能够补上。

————————————————
转载：
原文链接：

[吃透Java集合_吃透Java-CSDN博客](https://blog.csdn.net/u013277209/category_9385749.html)