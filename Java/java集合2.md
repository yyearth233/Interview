#### 重点：HashMap, ConcurrentHashMap, TreeMap

#### HashMap

```java
HashMap<String, Integer> hmap = new HashMap<>();

// Add elements to the map
hmap.put("a", 10);
hmap.put("b", 30);
hmap.put("c", 20);
hmap.put(null, 50);

// Print size and content
System.out.println("Size of map is: " + hmap.size());

System.out.println(hmap);  // {a=10, b=30, c=20}

if (hmap.containsKey("a")) {
    System.out.println("a: " + hmap.get("a"));
}

// 遍历
for (Map.Entry<String, Integer> en : hmap.entrySet()) {
    System.out.println(en.getKey() + ": " + en.getValue());
}
```
Constructor:
```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
static final float DEFAULT_LOAD_FACTOR = 0.75f;
transient Node<K,V>[] table;
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                            initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                            loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
```
存取 KV：
```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
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
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```
扩容：
```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
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
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                    (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
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

负载因子 load factor 与扩容机制有关。
扩容阈值 = 负载因子 * 当前数组大小
12 = 0.75 * 16，容量 size 达到 12 时会进行扩容。
负载因子为 1，数组全部填充后会进行扩容，此时很可能已经出现大量 hash 冲突，红黑树很大。空间利用率上去了，时间效率不高。
负载因子为 0.5，数组填充一半会扩容，此时 hash 冲突很小。空间利用率低，时间效率高。
负载因子 0.75 是平衡时间与空间：
```java
/*
* <p>As a general rule, the default load factor (.75) offers a good
* tradeoff between time and space costs.  Higher values decrease the
* space overhead but increase the lookup cost (reflected in most of
* the operations of the <tt>HashMap</tt> class, including
* <tt>get</tt> and <tt>put</tt>).  The expected number of entries in
* the map and its load factor should be taken into account when
* setting its initial capacity, so as to minimize the number of
* rehash operations.  If the initial capacity is greater than the
* maximum number of entries divided by the load factor, no rehash
* operations will ever occur.
*/
```

链表长度超过 8 个会转化为 红黑树。
```java
static final int TREEIFY_THRESHOLD = 8;
```

#### ConcurrentHashMap

```java
Map<String, Integer> chmap = new ConcurrentHashMap();
// chmap.put(null, "26");  // error 
```
不可以加入 null 值。
```java
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel);
```

1.7 版本：
Segment 分段锁机制保证线程安全，支持 concurrencyLevel (Segment 数组数量)数量的并发。
每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。

1.8 版本：
放弃 Segment 分段锁机制，采用了 CAS + synchronized 来保证并发安全性。





#### TreeMap

```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(100, "a");
map.put(102, "b");
map.put(101, "c");

for (Map.Entry<Integer, String> en : map.entrySet()) {
    System.out.println(en.getKey() + " " + en.getValue());
}
```

底层红黑树。
不能包含 null 值。

比较顺序定义：
key 实现 `compareable<?>` 接口。
自定义 TreeMap 中的 `private final Comparator<? super K> comparator`。

```java
public class People implements Comparable<People> {
    private String name;
    private int age;
    @Override
    public int compareTo(People o) {
        int num = this.name.compareTo(o.name);        // 姓名是主要条件
        return num == 0 ? this.age - o.age : num;     // 年龄是次要条件
        
    }
    ...
}
```

```java
TreeMap<String, String> tmap = new TreeMap<>(new Comparator<String>() {
    public int compare(String o1, String o2) {
        return o2.compareTo(o1);  // 表示倒序
    }
});

TreeMap<String, String> tMap = new TreeMap<>((o1, o2) -> {
    return o2.compareTo(o1);
});
```



