# 集合、IO

[TOC]

# 集合类

> 容器主要包括 Collection 和 Map 两种，Collection 存储着对象的集合，而 Map 存储着键值对(两个对象)的映射表。

## Collection 

### a. Set

Set:注重独一无二的性质,该体系集合可以知道某物是否已近存在于集合中,不会存储重复的元素

用于存储无序(存入和取出的顺序不一定相同)元素，值不能重复。

#### 1. HashSet

基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。

HashSet是通过对HashMap进行了一层包装而实现的,也就是说*HashSet*里面有一个*HashMap*(适配器模式)。

```java
//HashSet是对HashMap的简单包装
public class HashSet<E>
{
	......
	private transient HashMap<E,Object> map;//HashSet里面有一个HashMap
    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();
    public HashSet() {
        map = new HashMap<>();
    }
    ......
    public boolean add(E e) {//简单的方法转换
        return map.put(e, PRESENT)==null;
    }
    ......
}
```



#### 2. TreeSet

基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。

*TreeSet*和*TreeMap*二者在Java里有着相同的实现，==TreeSet仅仅是对TreeMap做了一层包装，也就是说TreeSet里面有一个TreeMap(适配器模式)。==

```java
// TreeSet是对TreeMap的简单包装
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
	......
    private transient NavigableMap<E,Object> m;
    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();
    public TreeSet() {
        this.m = new TreeMap<E,Object>();// TreeSet里面有一个TreeMap
    }
    ......
    public boolean add(E e) {
        return m.put(e, PRESENT)==null;
    }
    ......
}

```





#### 3. LinkedHashSet

具有 HashSet 的查找效率，且内部使用双向链表维护元素的插入顺序。

*LinkedHashSet*是对*LinkedHashMap*的简单包装，对*LinkedHashSet*的函数调用都会转换成合适的*LinkedHashMap*方法，因此*LinkedHashSet*的实现非常简单，这里不再赘述。

```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {
    ......
    // LinkedHashSet里面有一个LinkedHashMap
    public LinkedHashSet(int initialCapacity, float loadFactor) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
	......
    public boolean add(E e) {//简单的方法转换
        return map.put(e, PRESENT)==null;
    }
    ......
}
```





---

### b. List

List 是一个接口，它继承于`Collection`的接口。它代表着有序的队列。当我们讨论List的时候，一般都和Set作比较。

#### 1. ArrayList

基于动态数组实现，支持随机访问。

ArrayList实现了List接口，是顺序容器，即元素存放的数据与放进去的顺序相同，允许放入`null`元素，底层通过`数组实现`。

每个*ArrayList*都有一个容量(capacity)，表示底层数组的实际大小，容器内存储元素的个数不能多于当前容量。当向容器中添加元素时，如果容量不足，容器会自动增大底层数组的大小。

前面已经提过，Java泛型只是编译器提供的语法糖，所以这里的`数组是一个Object数组`，以便能够容纳任何类型的对象。

`add(int index, E e)` 需要先对元素进行移动，然后完成插入操作，也就意味着该方法有着线性的时间复杂度。

`trimToSize()` 将底层数组的容量调整为当前列表保存的实际元素的大小的功能。

##### 自动扩容机制

数组扩容通过一个公开的方法ensureCapacity(int minCapacity)来实现。在实际添加大量元素前，我也可以使用`ensureCapacity`来手动增加ArrayList实例的容量，以减少递增式再分配的数量。

==数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的1.5倍。==这种操作的代价是很高的，因此在实际使用时，我们应该尽量避免数组容量的扩张。当我们可预知要保存的元素的多少时，要在构造ArrayList实例时，就指定其容量，以避免数组扩容的发生。或者根据实际需求，通过调用ensureCapacity方法来手动增加ArrayList实例的容量。

扩容操作最终是通过`grow()`方法完成的,其核心代码如下:

```java
/**
* Increases the capacity to ensure that it can hold at least the
* number of elements specified by the minimum capacity argument.
*
* @param minCapacity the desired minimum capacity
*/
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





#### 2. LinkedList

基于`双向链表`实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。

不仅如此，LinkedList 还可以用作栈、队列和双向队列。

```JAVA
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

#### 3. Vector

和 ArrayList 类似，但它是线程安全的。



#### 4. Stack 和 Queue

Java里有一个叫做*Stack*的类，却没有叫做*Queue*的类(它是个接口名字)。

当需要使用栈时，==Java已不推荐使用*Stack*，而是推荐使用更高效的*ArrayDeque*==；

既然*Queue*只是一个接口，==当需要使用队列时也就首选*ArrayDeque*了(次选是*LinkedList*)==。

#### 5. *Deque*

*Deque*的含义是“double ended queue”，即双端队列，它既可以当作栈使用，也可以当作队列使用。

*ArrayDeque*底层通过数组实现，为了满足可以同时在数组两端插入或删除元素的需求，该数组还必须是循环的，即==循环数组(circular array)==，也就是说数组的任何一点都可能被看作起点或者终点。

==*ArrayDeque*是非线程安全的(not thread-safe)==，当多个线程同时使用的时候，需要程序员手动同步；另外，该容器不允许放入`null`元素。

其中,JDK中对于双端队列的插入很有一套:

```java
//addFirst(E e)
public void addFirst(E e) {
    if (e == null)//不允许放入null
        throw new NullPointerException();
    elements[head = (head - 1) & (elements.length - 1)] = e;//2.下标是否越界
    if (head == tail)//1.空间是否够用
        doubleCapacity();//扩容
}
```

**空间问题是在插入之后解决的**，因为`tail`总是指向下一个可插入的空位，也就意味着`elements`数组至少有一个空位，所以插入元素的时候不用考虑空间问题。

下标越界的处理: `head = (head - 1) & (elements.length - 1)`就可以了，**这段代码相当于取余，同时解决了`head`为负值的情况**。因为`elements.length`必需是`2`的指数倍(扩容决定的)，`elements - 1`就是二进制低位全`1`，跟`head - 1`相与之后就起到了取模的作用，如果`head - 1`为负数(其实只可能是-1)，则相当于对其取相对于`elements.length`的补码。

**扩容原理**

其逻辑是申请一个更大的数组(原数组的两倍)，然后将原数组复制过去。过程如下图所示:

<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/ArrayDeque_doubleCapacity.png" alt="ArrayDeque_doubleCapacity.png" style="zoom: 50%;" />

图中我们看到，==为了保持循环数组的性质,复制分两次进行，第一次复制*head*右边的元素，第二次复制*head*左边的元素。==



---

### c. Queue

Queue有两种常见的实现，LinkedList和PriorityQueue

#### LinkedList

前面已经介绍过。

#### PriorityQueue

基于堆结构实现，可以用它来实现优先队列。

==优先队列的作用是能保证每次取出的元素都是队列中权值最小的==(Java的优先队列每次取最小元素，C++的优先队列每次取最大元素)。这里牵涉到了大小关系，元素大小的评判可以通过元素本身的自然顺序(\*natural ordering\*)，==也可以通过构造时传入的比较器==(*Comparator*，类似于C++的仿函数)。

​	Java中*PriorityQueue*实现了*Queue*接口，不允许放入`null`元素；其通过堆实现，具体说是==通过完全二叉树(*complete binary tree*)实现的**小顶堆**(任意一个非叶子节点的权值，都不大于其左右子节点的权值)，也就意味着可以通过数组来作为*PriorityQueue*的底层实现。==

<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/PriorityQueue_base.png" alt="PriorityQueue_base.png" style="zoom:50%;" />

```java
leftNo = parentNo*2+1
rightNo = parentNo*2+2
parentNo = (nodeNo-1)/2
```

新加入的元素可能会破坏小顶堆的性质，因此需要进行必要的调整。源码中实现调整结构的代码如下:

```java
//siftUp()
private void siftUp(int k, E x) {
    while (k > 0) {
        int parent = (k - 1) >>> 1;//parentNo = (nodeNo-1)/2
        Object e = queue[parent];
        if (comparator.compare(x, (E) e) >= 0)//调用比较器的比较方法
            break;
        queue[k] = e;
        k = parent;
    }
    queue[k] = x;
}
```

==添加元素时,先默认添加到末尾,然后调整结构;删除堆顶元素时,先默认将最后一个元素放置到堆顶,然后再进行调整。==



## Map

### a. HashMap-JDK7

基于哈希表实现。

根据对冲突的处理方式不同，哈希表有两种实现方式，一种开放地址方式(Open addressing)，另一种是冲突链表方式(Separate chaining with linked lists)。==**Java7 HashMap采用的是冲突链表方式**。==

<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/HashMap_base.png" alt="HashMap_base" style="zoom: 50%;" />

​	HashMap的`put()`和`get()`方法可以在常数时间内完成。但在对*HashMap*进行迭代时，需要遍历整个table以及后面跟的冲突链表。==因此对于迭代比较频繁的场景，不宜将*HashMap*的初始大小设的过大。==

​	有两个参数可以影响*HashMap*的性能: `初始容量(inital capacity)`和`负载系数(load factor)`。初始容量指定了初始`table`的大小，负==载系数用来指定自动扩容的临界值。==当`entry`的数量超过`capacity * load_factor`时，容器将自动扩容并重新哈希。对于插入元素较多的场景，将初始容量设大可以减少重新哈希的次数。

将对象放入到*HashMap*或*HashSet*中时，有两个方法需要特别关心: `hashCode()`和`equals()`。

==hashCode()方法决定了对象会被放到哪个bucket里，==

==当多个对象的哈希值冲突时，equals()方法决定了这些对象是否是“同一个对象”。==

==所以，如果要将自定义的对象放入到HashMap或HashSet中，需要@Override  hashCode()和equals()方法。==



​	`get(Object key)`方法根据指定的`key`值返回对应的`value`，该方法调用了`getEntry(Object key)`得到相应的`entry`，然后返回`entry.getValue()`。因此`getEntry()`是算法的核心。 算法思想是首先通过`hash()`函数得到对应`bucket`的下标，然后依次遍历冲突链表，通过`key.equals(k)`方法来判断是否是要找的那个`entry`。

```java
// HashMap 查找
//getEntry()方法
final Entry<K,V> getEntry(Object key) {
	......
	int hash = (key == null) ? 0 : hash(key);
    for (Entry<K,V> e = table[hash&(table.length-1)];//得到冲突链表
         e != null; e = e.next) {//依次遍历冲突链表中的每个entry
        Object k;
        //依据equals()方法判断是否相等
        if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
            return e;
    }
    return null;
}
```

​	`put(K key, V value)`方法是将指定的`key, value`对添加到`map`里。该方法首先会对`map`做一次查找，看是否包含该元组，如果已经包含则直接返回，查找过程类似于`getEntry()`方法；如果没有找到，则会通过`addEntry(int hash, K key, V value, int bucketIndex)`方法插入新的`entry`，插入方式为**头插法**。

```java
//addEntry()
void addEntry(int hash, K key, V value, int bucketIndex) {
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);//自动扩容，并重新哈希
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = hash & (table.length-1);//hash%table.length
    }
    //在冲突链表头部插入新的entry
    Entry<K,V> e = table[bucketIndex];
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```



### b. HashMap-JDK8+

Java8 对 HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以==其由 **数组+链表+红黑树** 组成==。

根据 Java7 HashMap 的介绍，我们知道，==查找的时候，根据 hash 值我们能够快速定位到数组的具体下标，但是之后的话，需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决于链表的长度，为 O(n)。==

为了降低这部分的开销，在 Java8 中，==当链表中的元素达到了 8 个时，会将链表转换为**红黑树**，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)。==

来一张图简单示意一下吧：

<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/java-collection-hashmap8.png" alt="img" style="zoom: 50%;" />

简单分析一下插入操作的过程:

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

// 第四个参数 onlyIfAbsent 如果是 true，那么只有在不存在该 key 时才会进行 put 操作
// 第五个参数 evict 我们这里不关心
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    
    // 第一次 put 值的时候，会触发下面的 resize()，类似 java7 的第一次 put 也要初始化数组长度
    // 第一次 resize 和后续的扩容有些不一样，因为这次是数组从 null 初始化到默认的 16 或自定义的初始容量
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 找到具体的数组下标，如果此位置没有值，那么直接初始化一下 Node 并放置在这个位置就可以了
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);

    else {// 数组该位置有数据
        Node<K,V> e; K k;
        // 首先，判断该位置的第一个数据和我们要插入的数据，key 是不是"相等"，如果是，取出这个节点
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 如果该节点是代表红黑树的节点，调用红黑树的插值方法，本文不展开说红黑树
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 到这里，说明数组该位置上是一个链表
            for (int binCount = 0; ; ++binCount) {
                // 插入到链表的最后面(Java7 是插入到链表的最前面)
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // TREEIFY_THRESHOLD 为 8，所以，如果新插入的值是链表中的第 8 个
                    // 会触发下面的 treeifyBin，也就是将链表转换为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                // 如果在该链表中找到了"相等"的 key(== 或 equals)
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 此时 break，那么 e 为链表中[与要插入的新值的 key "相等"]的 node
                    break;
                p = e;
            }
        }
        // e!=null 说明存在旧值的key与要插入的key"相等"
        // 对于我们分析的put操作，下面这个 if 其实就是进行 "值覆盖"，然后返回旧值
        if (e != null) {
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 如果 HashMap 由于新插入这个值导致 size 已经超过了阈值，需要进行扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

resize() 方法用于初始化数组或数组扩容，每次扩容后，容量为原来的 2 倍，并进行数据迁移。







---

### c. TreeMap

基于红黑树实现。

TreeMap底层通过红黑树(Red-Black tree)实现，也就意味着`containsKey()`, `get()`, `put()`, `remove()`都有着`log(n)`的时间复杂度。

出于性能原因，*TreeMap*是非同步的(not synchronized)，如果需要在多线程环境使用，需要程序员手动同步；或者通过如下方式将*TreeMap*包装成(wrapped)同步的:

```java
SortedMap m = Collections.synchronizedSortedMap(new TreeMap(...));
```









### d. HashTable

和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用 ConcurrentHashMap 来支持线程安全，并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。

### e. LinkedHashMap

==使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用(LRU)顺序。==

*LinkedHashMap*实现了*Map*接口，即允许放入`key`为`null`的元素，也允许插入`value`为`null`的元素。从名字上可以看出该容器是*linked list*和*HashMap*的混合体，也就是说它同时满足*HashMap*和*linked list*的某些特性。

==可将LinkedHashMap看作采用linked list增强的HashMap。==

<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/LinkedHashMap_base.png" alt="LinkedHashMap_base.png" style="zoom:67%;" />



​	事实上*LinkedHashMap*是*HashMap*的直接子类，二者唯一的区别是==LinkedHashMap在HashMap的基础上，采用双向链表(doubly-linked list)的形式将所有entry连接起来，这样是为保证元素的迭代顺序跟插入顺序相同。==上图给出了*LinkedHashMap*的结构图，主体部分跟*HashMap*完全一样，多了`header`指向双向链表的头部(是一个哑元)，该双向链表的迭代顺序就是`entry`的插入顺序。

​	除了可以保迭代历顺序，这种结构还有一个好处 : ==迭代LinkedHashMap时不需要像HashMap那样遍历整个table，而只需要直接遍历header指向的双向链表即可==，也就是说*LinkedHashMap*的迭代时间就只跟`entry`的个数相关，而跟`table`的大小无关。

​	出于性能原因，*LinkedHashMap*是非同步的(not synchronized)，如果需要在多线程环境使用，需要程序员手动同步；或者通过如下方式将*LinkedHashMap*包装成(wrapped)同步的:

```
Map m = Collections.synchronizedMap(new LinkedHashMap(...));
```

与HashMap不同的是,对插入和删除操作来说:

1. 从`table`的角度看，新的`entry`需要插入(或删除)到对应的`bucket`里，当有哈希冲突时，采用头插法将新的`entry`插入(或删除)到冲突链表的头部。
2. 从`header`的角度看，新的`entry`需要插入(或删除)到双向链表的尾部。

`put(K key, V value)`方法是将指定的`key, value`对添加到`map`里。该方法首先会对`map`做一次查找，看是否包含该元组，如果已经包含则直接返回，查找过程类似于`get()`方法；如果没有找到，则会通过`addEntry(int hash, K key, V value, int bucketIndex)`方法插入新的`entry`。

```java
// LinkedHashMap.addEntry()
void addEntry(int hash, K key, V value, int bucketIndex) {
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);// 自动扩容，并重新哈希
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = hash & (table.length-1);// hash%table.length
    }
    // 1.在冲突链表头部插入新的entry
    HashMap.Entry<K,V> old = table[bucketIndex];
    Entry<K,V> e = new Entry<>(hash, key, value, old);
    table[bucketIndex] = e;
    // 2.在双向链表的尾部插入新的entry
    e.addBefore(header);
    size++;
}
```





# I/O

## a. 本地IO

### 1. IO理解分类 - 从传输方式上

从数据传输方式或者说是运输方式角度看，可以将 IO 类分为:

- 字节流
- 字符流

`字节`是个计算机看的，`字符`才是给人看的

### 2. 字节流和字符流的区别

- 字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节。)
- 字节流用来处理二进制文件(图片、MP3、视频文件)，字符流用来处理文本文件(可以看做是特殊的二进制文件，使用了某种编码，人可以阅读)。



<img src="asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/image-20220311172058164.png" alt="image-20220311172058164" style="zoom:50%;" />

```java
/*
 *  流的体系结构
 *  抽象基类             节点流                缓存流
 *  InputStream         FileInputStream     BufferedInputStream
 *  OutputStream        FileOutputStream    BufferedOutputSteam
 *  Reader              FileReader          BufferedReader
 *  Writer              FileWriter          BufferedWriter
 * */
```

### 3. FileReader

```java
// 测试 FileReader
// 为了保证资源一定可以被关闭,使用try-catch-finally来关闭资源
public static void testFileReader() {
    FileReader fr = null;
    try {
        // 1. 实例化File类对象
        File file = new File("Interview\\hello.txt"); // 路径相较于当前工程
        // 2. 提供具体的流
        fr = new FileReader(file);
        // 3. 数据的读入过程
        // RETURN The character read, or -1 if the end of the stream has been reached
        // 返回一个读入的字符,如果到达文件末尾返回-1
        int data;
        while ((data = fr.read()) != -1) {
            System.out.print((char) data);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            // 4. 关闭流操作
            // JVM 虽然有垃圾回收机制,但对于物理连接无能为力,比如: 数据库连接、输入输出流、Socket
            if (fr != null) fr.close();
            // 因为 fr可能在前面由于打开失败而为null,不能直接close
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

```java
public static void testFileReader1() {
    FileReader fr = null;
    try {
        File file = new File("Interview//hello.txt");
        fr = new FileReader(file);
        char[] cBuf = new char[5];
        // RETURN   The number of characters read,
        //          or -1 if the end of the stream has been reached
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            //错误写法1
            //System.out.println(Arrays.toString(cBuf));//
            //错误写法2
            //for (int i = 0; i < cBuf.length; i++) {
            //   System.out.print(cBuf[i]);
            //}
            // 正确1
            for (int i = 0; i < len; i++) {
                System.out.print(cBuf[i]);
            }
            // 正确2
            String s = new String(cBuf, 0, len);
            System.out.print(s);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 4. FileWriter

```java
	// 从内存中写出数据到磁盘的文件里。
	/*
     *  输出操作 对应File可以不存在,会自动创建,不会爆异常
     *          如果存在,可以根据第二个参数确定是否追加,默认不追加,进行覆盖
     *          FileWriter fw = new FileWriter(file,false/true);
     * */
public static void TestFileWriter() throws IOException {

    FileWriter fw = null;
    try {
        File file = new File("Interview//IO//helloWriter.txt");
        System.out.println(file.getAbsolutePath());
        // file文件本身没有写出的能力,
        fw = new FileWriter(file, false);
        fw.write("Fuck F4N!");
        fw.write("2022-3-11");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (fw != null) fw.close();
    }
}
```

### 5. 复制操作

```java
// 对文件的读写,做一个复制操作
public static void TestFileReaderWriter() throws IOException {
    FileReader fr = null;
    FileWriter fw = null;
    try {
        File srcFile = new File("Interview//IO//hello.txt");
        File destFile = new File("Interview//IO//helloWriter.txt");


        fr = new FileReader(srcFile);
        fw = new FileWriter(destFile,true);
        char[] cBuf = new char[5];
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            fw.write(cBuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fw != null) fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 6. FileInputOutputStream处理图片

处理图片、视频等需要用字节流，字节流处理字符可能出现乱码问题，尤其是中文。但==实际上，如果只进行复制操作，而在程序中不进行加工，就没什么影响。==

文本文件：.txt .java .c .cpp ...

非文本文件: .jpg .mp3 .doc .ppt ...

```java
 // 使用字节流处理图片
public static void ImgTestFileInputOutputStream   () throws IOException {
    FileInputStream fr = null;
    FileOutputStream fw = null;
    try {
        File srcFile = new File("Interview//IO//testimg.jpg");
        File destFile = new File("Interview//IO//copyimg.jpg");

        fr = new FileInputStream(srcFile);
        fw = new FileOutputStream(destFile);
        byte[] cBuf = new byte[5];
        int len;
        while ((len = fr.read(cBuf)) != -1) {
            fw.write(cBuf, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fw != null) fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            if (fr != null) fr.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 7. 缓冲流

```
BufferedInputStream
BufferedOutputSteam
BufferedReader
BufferedWriter
```

==缓冲流使用要先套接到已有的"流"之上==

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(new File("Interview//IO//testimg.jpg")));
```

如果使用`BufferedReader`和`BufferedWriter`,可以使用String进行读取。

开发会优先使用缓冲流， 因为==缓冲流内部提供了缓冲区== ，速度会快很多。

```java
public static void TestBufferStream() throws IOException {
    /*非文本文件的复制*/
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    try {
        // 造文件
        File srcFile = new File("Interview//IO//testimg.jpg");
        File destFile = new File("Interview//IO//CopyIMG.jpg");
        // 造节点流
        FileInputStream fis = new FileInputStream(srcFile);
        FileOutputStream fos = new FileOutputStream(destFile);
        // 造缓冲流
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);
        // 简写
        // bis = new BufferedInputStream(new FileInputStream(new File("Interview//IO//testimg" +
        //                    ".jpg")));
        // bos = new BufferedOutputStream(new FileOutputStream(new File("Interview//IO//CopyIMG" +
        //                    ".jpg")));

        byte[] buffer = new byte[10];
        int len;
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        // 资源关闭
        // 先关外层的流,再关内层的流
        bis.close();
        bos.close();
        // 其实,关闭外层流时,会自动关闭内层流,也就是说下面的内层的流可以省略
        // fis.close();
        // fos.close();
    }
}
```

###  8. 统计一个文件中字符出现次数

```java
// 统计 comment.txt中每个字符出现的次数
public static void wordCount() throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(new File("Interview//IO//comment.txt")));
    BufferedWriter bw = new BufferedWriter(new FileWriter(new File("Interview//IO//commentCount.txt")));
    HashMap<Character, Integer> hashMap = new HashMap<>();
    int ch;
    while ((ch = br.read()) != -1) {
        char c = (char) ch;
        hashMap.put(c, hashMap.getOrDefault(c, 0) + 1);
    }
    br.close();
    for (Map.Entry<Character, Integer> en : hashMap.entrySet()) {
        char c = en.getKey();
        switch (c){
            case ' ':
                bw.write("空格 = "+en.getValue());
                break;
            case '\t':
                bw.write("tab = "+en.getValue());
                break;
            case '\r':
                bw.write("回车 = "+en.getValue());
                break;
            case '\n':
                bw.write("换行 = "+en.getValue());
                break;
            default:
                bw.write(c+" = "+en.getValue());
                break;
        }
        bw.newLine();
    }
    bw.close();
}
```

### 9. 转换流

==转换流提供了 字节流 到 字符流 的转换。==

```
/*
 *  转换流:
 *  InputStreamReader ： 将一个字节的输入流 转换为 字符 的输入流
 *  OutputStreamWriter ：将一个字符的输出流 转换为 字节 的输出流
 *  实现了 字节流 到 字符流之间的转换。
 *
 *  从 字节 --> 字符 是一种解码
 *  从 字符 --> 字节 是一种编码
 *
 *  这个过程存在字符集的设置问题
 * */
```

```java
public static void main(String[] args) throws IOException {
    /* 实现字节流到输入流的转换 */
    FileInputStream fis = new FileInputStream("Interview//IO//comment.txt");
    FileOutputStream fos = new FileOutputStream("Interview//IO//commentGBK.txt");
    // 参数二指明字符集,根据读取的文件字符集来确定
    InputStreamReader isr = new InputStreamReader(fis, "UTF-8");
    OutputStreamWriter osw = new OutputStreamWriter(fos, "GBK");
    char[] cbuf = new char[20];
    int len;
    while ((len = isr.read(cbuf)) !   = -1) {
        String s = new String(cbuf, 0, len);
        System.out.print(s);
        osw.write(s);
    }
    isr.close();
    osw.close();
}
```
### 10. 标准的输入输出流

```java
/*
 *  标准的输入流
 *  System.in: 标准的输入流,默认从键盘输入
 *  System.out: 标准的输出流,默认从控制台输出
 *
 *  System类的setIn(InputStream is) / setOut(PrintStream ps)方式重新指定输入输出的流
* */
```

```java
// 实现一个输入转为大写的程序
// 1. 用Scanner实现
// 2. 用System.in实现
//    System.in --> 转换流 --> BufferedReader 的 readLine()

InputStreamReader isr = new InputStreamReader(System.in);
BufferedReader br = new BufferedReader(isr);
while (true){
    String s = br.readLine();
    if ("e".equalsIgnoreCase(s) || "exit".equalsIgnoreCase(s) ){
        System.out.println("EXIT.");
        break;
    }
    System.out.println(s.toUpperCase());
}
br.close();
```



### 11. 打印流

```java
// 打印流
// PrintStream PrintWriter
// 它们提供了一系列的 print println方法
// 可以使用System.setOut()设置答应的目标,默认为显示器
FileOutputStream fos = new FileOutputStream(new File("Interview//IO//hello.txt"));
PrintStream stream = new PrintStream(fos);
// 把输入目的地改为文件,而不是显示器
if (stream != null) System.setOut(stream);
System.out.println();
```

### 12. 数据流

主要是为了 ==为了方便操作Java语言中的基本类型和 String==

```java
//        DataInputStream 套接到 InputStream
//        DataOutputStream 套接到 OutputStream
DataOutputStream dos = new DataOutputStream(new FileOutputStream("Interview//IO//hello.txt"));
dos.writeUTF("中国");
// 刷新操作 flush-冲洗
dos.flush();
dos.writeInt(23);
dos.flush();
dos.writeBoolean(true);
dos.close();

DataInputStream dis = new DataInputStream(new FileInputStream("Interview//IO//hello.txt"));
String country = dis.readUTF();
int age = dis.readInt();
boolean b = dis.readBoolean();
System.out.println(country+" "+age+" "+b);
dis.close();
```

### 13. 对象流:

`ObjectInputStream`和`ObjectOutputStream`

*  用以存储和读取 对象类型 和 基本类型 的处理流
*  通过 `序列化` 与 `反序列化` 来进行对对象数据的 保存、传输、读取。
*  `ObjectOutputStream` 和 `ObjectInputStream` 不能序列化 static和transient 修饰的成员变量
*  对象序列化 : 将内存中的Java对象转换为平台无关的二进制流 这样就可以将对象进行与平台无关的 传输和保存
*  实现了Serializable接口的对象 可以转化为 字节数据

```java
// Object 类型的写入
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("Interview//IO//Object.dat"));
oos.writeObject(new String("我爱北京"));
oos.flush();
oos.close();

// Object 读入
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("Interview//IO//Object.dat"));
String str =(String) ois.readObject();
System.out.println(str);
ois.close();
```

自定义对象实现 `Serializable接口` 来进行序列化和反序列化,需要提供一个`serialVersionUID`来进行一个标识,确保可以还原和区分。与此同时，==必须保证自定义的类的属性字段也都可以序列化==，基础类型默认可以序列化。

```java
class Person implements Serializable {
    // 序列版本号
   
    private static final long serialVersionUID = 46516164984L;
    private String name;
    private int age;

    @Override
    public String toString() {
        return "Person{" +
            "name='" + name + '\'' +
            ", age=" + age +
            '}';
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

`serialVersionUID`使用来表示类的不同版本间的兼容性，如果没有显示定义，JVM会自动生成一个，但这样可能会在修改类时产生问题，导致序列化失败。  



### 14. 随机存取文件流

`RandomAccessFile` 直接继承 Object , 随机存取文件流的特殊指出在于 ==既可以作为输入流,也可以作为输出流==。

如果文件已经存在,写时会从头进行覆盖。

- 可以使用`seek()`函数进行指针的移动。
- 要实现插入需要先将后面的数据向后移动再插入。

如果文件不存在，则会自动创建。

### 基本知识点汇总

1. 流的三种分类方式

   - 流向: 输入流、输出流
   - 数据单位：字节流、字符流
   - 流的角色：节点流、处理流

2. 写出4个IO流中的抽象基类,4个文件流,4个缓冲流

   抽象基类:

   - InputStream
   - OutputStream
   - Reader
   - Writer

   文件流:

   - FileReader
   - FileWriter
   - FileInputStream
   - FileOutputStream

   缓冲流:

   - BufferedReader
   - BufferedWriter
   - BufferedInputStream
   - BufferedOutputStream

   转换流:`InputSteamReader` `OutputStreamWriter`

3. 字符流和字节流分别的使用情景

   字节流: 主要用来处理非文本文件

   字符流: 主要用来处理文本文件

## b. BIO NIO AIO 异同

BIO: `同步阻塞型`。 服务器为客户端一个连接一个线程，数据未准备好时阻塞。

- 适用于连接数目少，并固定的架构，这种方式对服务器资源要求比较高，低并发的情况下。JDK1.4之前的唯一选择

NIO: `同步非阻塞型`。由一个中央选择器进行IO请求的接收，并`轮询访问`是否有已经准备好的IO请求，有就会去处理。

- 适用于连接数目较多，但单个连接比较短的情况，比如聊天服务器、弹幕系统、服务器通讯等.JDK1.4开始支持

AIO: `异步非阻塞`。客户端IO请求先由操作系统(OS)进行处理,数据处理好后`通知服务器`处理程序来进行处理。

- 适用于 连接数目多,且连接较长(重操作)的架构,比如相册服务器,充分发挥OS参加IO操作,比较复杂,JDK7开始支持

在文中我们一再说明JAVA AIO框架在windows下使用windows IOCP技术，在Linux下使用epoll多路复用IO技术模拟异步IO，这个从JAVA AIO框架的部分类设计上就可以看出来。

| BIO            | NIO                   | AIO                               |
| -------------- | --------------------- | --------------------------------- |
| `Socket`       | `SocketChannel`       | `AsynchronousSocketChannel`       |
| `ServerSocket` | `ServerSocketChannel` | `AsynchornousServerSocketChannel` |



## c. Unix IO 类型

一个输入操作通常包括两个阶段:

- 等待数据准备好
- 从内核向进程复制数据

​	对于一个套接字上的输入操作，第一步通常涉及等待数据从网络中到达。当所等待分组到达时，它被复制到内核中的某个缓冲区。第二步就是把数据从内核缓冲区复制到应用进程缓冲区。

Unix 下有五种 I/O 模型:

- 阻塞式 I/O
- 非阻塞式 I/O
- I/O 复用(select 和 poll)
- 信号驱动式 I/O(SIGIO)
- 异步 I/O(AIO)

### 1. 阻塞式IO

==应用进程被阻塞==，直到数据复制到应用进程缓冲区中才返回。

应该注意到，在阻塞的过程中，其它程序还可以执行，因此==阻塞不意味着整个操作系统都被阻塞==。因为其他程序还可以执行，因此不消耗 CPU 时间，这种模型的==执行效率会比较高。==

### 2. 非阻塞式 I/O

应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是==需要不断的执行系统调用来获知 I/O 是否完成==，这种方式称为`轮询(polling)`。

由于 CPU 要处理更多的系统调用，因此这种模型是比较==低效==的。

### 3. I/O 复用

使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读，这一过程会被阻塞，当某一个套接字可读时返回。之后再使用 recvfrom 把数据从内核复制到进程中。

它可以让==单个进程具有处理多个 I/O 事件的能力==。又被称为 Event Driven I/O，即事件驱动 I/O。

如果一个 Web 服务器没有 I/O 复用，那么每一个 Socket 连接都需要创建一个线程去处理。如果同时有几万个连接，那么就需要创建相同数量的线程。并且相比于多进程和多线程技术，I/O 复用不需要进程线程创建和切换的开销，系统开销更小。



### 4. 信号驱动 I/O

应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说==等待数据阶段应用进程是非阻塞的==。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中。

==相比于非阻塞式 I/O 的轮询方式，信号驱动 I/O 的 CPU 利用率更高==。



### 5. 异步 I/O

进行 aio_read 系统调用会立即返回，应用进程继续执行，不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。

异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O。



## e. BIO

BIO就是: blocking IO。最容易理解、最容易实现的IO工作方式，==应用程序向操作系统请求网络IO操作，这时应用程序会一直等待；==另一方面，==操作系统收到请求后，也会等待数据完成==，直到网络上有数据传到监听端口；操作系统在收集数据后，会把数据发送给应用程序；最后应用程序受到数据，并解除等待状态。

- `阻塞IO` 和 `非阻塞IO`

​	这两个概念是`程序级别`的。主要描述的是==程序请求操作系统IO操作后，如果IO资源没有准备好，那么程序该如何处理的问题==: 前者等待；后者继续执行(并且使用线程一直轮询，直到有IO资源准备好了)

- `同步IO` 和 `非同步IO`

​	这两个概念是`操作系统级别`的。主要描述的是==操作系统在收到程序请求IO操作后，如果IO资源没有准备好，该如何响应程序的问题==: 前者不响应，直到IO资源准备好以后；后者返回一个标记(好让程序和自己知道以后的数据往哪里通知)，当IO资源准备好以后，再用事件机制返回给程序。

### 1. 传统BIO

以前大多数网络通信方式都是阻塞模式的，即:

- 客户端向服务器端发出请求后，客户端会一直等待(不会再做其他事情)，直到服务器端返回结果或者网络出现问题。
- 服务器端同样的，当在处理某个客户端A发来的请求时，另一个客户端B发来的请求会等待，直到服务器端的这个处理线程完成上一个处理。

### 2. 传统的BIO的问题

- 同一时间，服务器只能接受来自于客户端A的请求信息；虽然客户端A和客户端B的请求是同时进行的，但客户端B发送的请求信息只能等到服务器接受完A的请求数据后，才能被接受。
- 由于服务器一次只能处理一个客户端请求，当处理完成并返回后(或者异常时)，才能进行第二次请求的处理。很显然，这样的处理方式在高并发的情况下，是不能采用的。

### 3. 多线程方式 - 伪异步方式

上面说的情况是服务器只有一个线程的情况，那么读者会直接提出我们可以使用多线程技术来解决这个问题:

- 当服务器收到客户端X的请求后，(读取到所有请求数据后)将这个请求送入一个独立线程进行处理，然后主线程继续接受客户端Y的请求。
- 客户端一侧，也可以使用一个子线程和服务器端进行通信。这样客户端主线程的其他工作就不受影响了，当服务器端有响应信息的时候再由这个子线程通过 监听模式/观察模式(等其他设计模式)通知主线程。

但是使用线程来解决这个问题实际上是有==局限性==的:

- 虽然在服务器端，请求的处理交给了一个独立线程进行，但是操作系统通知accept()的方式还是单个的。也就是，==实际上是服务器接收到数据报文后的“业务处理过程”可以多线程，但是数据报文的接受还是需要一个一个的来==(下文的示例代码和debug过程我们可以明确看到这一点)
- ==在linux系统中，可以创建的线程是有限的。==我们可以通过cat /proc/sys/kernel/threads-max 命令查看可以创建的最大线程数。当然这个值是可以更改的，但是线程越多，CPU切换所需的时间也就越长，用来处理真正业务的需求也就越少。
- 创建一个线程是有较大的资源消耗的。
- 如果您的应用程序大量使用长连接的话，线程是不会关闭的。这样系统资源的消耗更容易失控。



==BIO的问题关键不在于是否使用了多线程(包括线程池)处理这次请求，而在于accept()、read()的操作点都是被阻塞。==

即: 异步IO模式 就是为了解决这样的并发性存在的。

## f. NIO

通俗理解: NIO 是可以做到用一个线程来处理多个请求的操作。假如有1000个请求，可以根据实际情况，分配20~80个线程来进行处理，不需要像阻塞式IO非要分配1000个线程。

![image-20220315154500329](asset/%E5%86%85%E9%83%A8%E7%B1%BB%E3%80%81%E9%9B%86%E5%90%88%E3%80%81IO.assets/image-20220315154500329.png)

新的输入/输出 (NIO) 库是在 JDK 1.4 中引入的，弥补了原来的 I/O 的不足，==提供了高速的、面向块的 I/O。==

I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。

- 面向流的 I/O 一次处理一个字节数据: 一个输入流产生一个字节数据，一个输出流消费一个字节数据。为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责复杂处理机制的一部分。不利的一面是，面向流的 I/O 通常相当慢。

- 面向块的 I/O ==一次处理一个数据块==，按块处理数据比按流处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。

I/O 包和 NIO 已经很好地集成了，java.io.* 已经以 NIO 为基础重新实现了，所以现在它可以利用 NIO 的一些特性。例如，java.io.* 包中的一些类包含以块的形式读写数据的方法，这使得即使在面向流的系统中，处理速度也会更快。



NIO 与普通 I/O 的区别主要有以下两点:

- ==NIO 是非阻塞的==
- ==NIO 面向块，I/O 面向流==



### 1. 通道与缓冲区

通道 Channel 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。

通道与流的不同之处在于，==流只能在一个方向上移动==(一个流必须是 InputStream 或者 OutputStream 的子类)，而==通道是双向的，可以用于读、写或者同时用于读写。==

通道包括以下类型:

- ==FileChannel: 从文件中读写数据；==
- DatagramChannel: 通过 UDP 读写网络中数据；
- SocketChannel: 通过 TCP 读写网络中数据；
- ServerSocketChannel: 可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

---

发送给一个通道的所有数据都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要先读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区。

缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。

缓冲区包括以下类型:

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer

---

使用 NIO 快速复制文件的实例:

```java
private static final String FILE_PATH = "Interview//NIO//";
public static void main(String[] args) throws IOException {
    /*输入字节流*/
    FileInputStream fin = new FileInputStream(FILE_PATH + "hello.txt");
    /*输入通道*/
    FileChannel fcin = fin.getChannel();
    /*输出管道*/
    FileOutputStream fout = new FileOutputStream(FILE_PATH + "out.txt");
    /*输出字节流*/
    FileChannel fcout = fout.getChannel();
    /* 为缓冲区分配 1024 个字节 */
    ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
    while (true) {
        int read = fcin.read(buffer);
        if (read == -1) break;
        /*切换读写*/
        buffer.flip();
        /* 把缓冲区的内容写入输出文件中 */
        fcout.write(buffer);
        /* 清空缓冲区 */
        buffer.clear();
    }
    fcout.close();
    fcin.close();
}
```



### 2. Selector(选择器)

有Selector选择器来在收到访问后,轮询的查找是否已经有准备好的IO请求可以进行返回处理,如果有,则去单独阻塞的处理请求。

### 3. NIO - IO多路复用详解

目前流程的多路复用IO实现主要包括四种: `select`、`poll`、`epoll`、`kqueue`。下表是他们的一些重要特性的比较:

| IO模型 | 相对性能 | 关键思路         | 操作系统      | JAVA支持情况                                                 |
| ------ | :------- | ---------------- | ------------- | ------------------------------------------------------------ |
| select | 较高     | Reactor          | windows/Linux | 支持,Reactor模式(反应器设计模式)。Linux操作系统的 kernels 2.4内核版本之前，默认使用select；而目前windows下对同步IO的支持，都是select模型 |
| poll   | 较高     | Reactor          | Linux         | Linux下的JAVA NIO框架，Linux kernels 2.6内核版本之前使用poll进行支持。也是使用的Reactor模式 |
| epoll  | 高       | Reactor/Proactor | Linux         | Linux kernels 2.6内核版本及以后使用epoll进行支持；Linux kernels 2.6内核版本之前使用poll进行支持；另外一定注意，由于Linux下没有Windows下的IOCP技术提供真正的 异步IO 支持，所以Linux下使用epoll模拟异步IO |
| kqueue | 高       | Proactor         | Linux         | 目前JAVA的版本不支持                                         |

多路复用IO技术最适用的是“高并发”场景，所谓高并发是指1毫秒内至少同时有上千个连接请求准备好。其他情况下多路复用IO技术发挥不出来它的优势。另一方面，使用JAVA NIO进行功能实现，相对于传统的Socket套接字实现要复杂一些，所以实际应用中，需要根据自己的业务需求进行技术选择。



## g. AIO

`异步非阻塞`。客户端IO请求先由操作系统(OS)进行处理,数据处理好后`通知服务器`处理程序来进行处理。

当进行读写操作时，只需要直接调用API的read和write方法即可，这两种方法均是异步的。

- 适用于 连接数目多,且连接较长(重操作)的架构,比如相册服务器,充分发挥OS参加IO操作,比较复杂,JDK7开始支持



















