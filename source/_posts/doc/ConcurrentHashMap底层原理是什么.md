---
title: ConcurrentHashMap底层原理是什么
category: 文档
tag: 总结
date: 2024-02-14 11:00:00
abbrlink: 3
---
## ConcurrentHashMap底层原理是什么

- 1.7 数据结构：
    内部主要是一个Segment数组，而数组的每一项又是一个HashEntry数组，元素都存在HashEntry数组里。因为每次锁定的是Segment对象，也就是整个HashEntry数组，所以又叫分段锁。
![1.7ConcurrentHashMap.png](/img/images/1.7ConcurrentHashMap.png)
- 1.8 数据结构：
    与HashMap一样采用：数组+链表+红黑树
    ![ConCurrentHashMap.png](/img/images/ConCurrentHashMap.png)
    底层原理则是采用锁链表或者红黑树头结点，相比于HashTable的方法锁，力度更细，是对数组（table）中的桶（链表或者红黑树）的头结点进行锁定，这样锁定，只会影响数组（table）当前下标的数据，不会影响其他下标节点的操作，可以提高读写效率。
putVal执行流程：

1. 判断存储的key、value是否为空，若为空，则抛出异常
1. 计算key的hash值，随后死循环（该循环可以确保成功插入，当满足适当条件时，会主动终止），判断table表为空或者长度为0，则初始化table表
1. 根据hash值获取table中该下标对应的节点，如果该节点为空，则根据参数生成新的节点，并以CAS的方式进行更新，并终止死循环。
1. 如果该节点的hash值是MOVED(-1)，表示正在扩容，则辅助对该节点进行转移。
1. 对数组（table）中的节点，即桶的头结点进行**锁定**，如果该节点的hash大于等于0，表示此桶是链表，然后对该桶进行遍历（死循环），寻找链表中与put的key的hash值相等，并且key相等的元素，然后进行值的替换，如果到链表尾部都没有符合条件的，就新建一个node，然后插入到该桶的尾部，并终止该循环遍历。
1. 如果该节点的hash小于0，并且节点类型是TreeBin，则走红黑树的插入方式。
1. 判断是否达到转化红黑树的阈值，如果达到阈值，则链表转化为红黑树。