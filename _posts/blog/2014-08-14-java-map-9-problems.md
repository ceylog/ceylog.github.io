---
layout:     post
title:      Java中关于Map的九大问题
category: blog
description: Java中关于Map的九大问题

---

通常来说，Map是一个由键值对组成的数据结构，且在集合中每个键是唯一的。下面就以K和V来代表键和值，来说明一下java中关于Map的九大问题。

## 1、将Map转换为List类型

在java中Map接口提供了三种集合获取方式：Key set,，value set， and key-value set.。它们都可以通过构造方法或者addAll()方法来转换为List类型。下面代码就说明了如何从Map中构造ArrayList：

    // key list
    List keyList = new ArrayList(map.keySet());
    // value list
    List valueList = new ArrayList(map.valueSet());
    // key-value list
    List entryList = new ArrayList(map.entrySet());

## 2、通过Entry 遍历Map

java中这种以键值对存在的方式被称为Map.Entry。Map.entrySet()返回的是一个key-value 集合，这是一种非常高效的遍历方式。

    for(Entry entry: map.entrySet()) {
        // get key
        K key = entry.getKey();
        // get value
        V value = entry.getValue();
    }

Iterator 我们也经常用到，尤其是在JDK1.5以前

    Iterator itr = map.entrySet().iterator();
    while(itr.hasNext()) {
        Entry entry = itr.next();
        // get key
        K key = entry.getKey();
        // get value
        V value = entry.getValue();
    }

## 3、通过Key来对Map排序

排序需要对Map的ke进行频繁的操作，一种方式就是通过比较器(comparator )来实现：

    List list = new ArrayList(map.entrySet());
    Collections.sort(list, new Comparator() {
        @Override
        public int compare(Entry e1, Entry e2) {
            return e1.getKey().compareTo(e2.getKey());
        }
    });

另外一种方法就是通过SortedMap，但必须要实现Comparable接口。

    SortedMap sortedMap = new TreeMap(new Comparator() {
        @Override
        public int compare(K k1, K k2) {
            return k1.compareTo(k2);
        }
    });
    sortedMap.putAll(map);

## 4、对value对Map进行排序

这与上一点有些类似，代码如下：

    List list = new ArrayList(map.entrySet());
    Collections.sort(list, new Comparator() {
        @Override
        public int compare(Entry e1, Entry e2) {
            return e1.getValue().compareTo(e2.getValue());
        }
    });

## 5、初始化一个static 的常量Map

当你希望创建一个全局静态Map的时候，我们有以下两种方式，而且是线程安全的。
而在Test1中，我们虽然声明了map是静态的，但是在初始化时，我们依然可以改变它的值，就像Test1.map.put(3,”three”);
在Test2中，我们通过一个内部类，将其设置为不可修改，那么当我们运行Test2.map.put(3,”three”)的时候，它就会抛出一个UnsupportedOperationException 异常来禁止你修改。
    public class Test1 {
        private static final Map map;
        static {
            map = new HashMap();
            map.put(1, “one”);
            map.put(2, “two”);
        }
    }

    public class Test2 {
        private static final Map map;
        static {
            Map aMap = new HashMap();
            aMap.put(1, “one”);
            aMap.put(2, “two”);
            map = Collections.unmodifiableMap(aMap);
        }
    }

## 6、HashMap, TreeMap, and Hashtable之间的不同

在Map接口中，共有三种实现：HashMap，TreeMap，Hashtable。

它们之间各有不同，详细内容请参考《 HashMap vs. TreeMap vs. Hashtable vs. LinkedHashMap》一文。


## 6、Map中的反向查询

我们在Map添加一个键值对后，意味着这在Map中键和值是一一对应的，一个键就是对应一个值。但是有时候我们需要反向查询，比如通过某一个值来查找它的键，这种数据结构被称为bidirectional map，遗憾的是JDK并没有对其支持。

Apache和Guava 共同提供了这种bidirectional map实现，它在实现中它规定了键和值都是必须是1:1的关系。


## 7、对Map的复制

java中提供了很多方法都可以实现对一个Map的复制，但是那些方法不见得会时时同步。简单说，就是一个Map发生的变化，而复制的那个依然保持原样。下面是一个比较高效的实现方法：
    
    Map copiedMap = Collections.synchronizedMap(map);

当然还有另外一个方法，那就是克隆。但是我们的java鼻祖Josh Bloch却不推荐这种方式，他曾经在一次访谈中说过关于Map克隆的问题：在很多类中都提供了克隆的方法，因为人们确实需要。但是克隆非常有局限性，而且在很多时候造成了不必要的影响。（原文《Copy constructor versus cloning》）


## 8、创建一个空的Map


如果这个map被置为不可用，可以通过以下实现

    map = Collections.emptyMap();

相反，我们会用到的时候，就可以直接

    map = new HashMap();


文章源自：IT学习者