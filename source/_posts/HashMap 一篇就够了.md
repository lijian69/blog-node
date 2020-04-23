---

title: HashMap 一篇就够了  
categories: 后台开发   
tags:

  - java
  - HashMap
  - 集合

---

为什么要学习 HashMap ？这你就不用问我了。如果你不知道为什么？那证明你不是一个真正的 Java 开发工程师。假的？学透请随我来~。



### 一篇文章学会 HashMap？

![欢迎阅览](https://pic.downk.cc/item/5ea0f4aec2a9a83be57836d3.gif)

---
>作者介绍：
> 本人Java特工，代号：Cris Li ； 中文名：克瑞斯理
> 简书地址: [https://www.jianshu.com/u/c508b0afaaee](https://www.jianshu.com/u/c508b0afaaee)
> CSDN地址: [https://blog.csdn.net/jianli95](https://blog.csdn.net/jianli95)
> 个人纯洁版博客: https://lijiangit.gitee.io/
---

## Q1：你用过 HashMap 吗？，你能跟我说说它的数据结构吗？
**回答：** HashMap 是一种容器类型，通过 Key-Value 键值对存储数据，JDK1.8 之前采用 `数组+链表` 的数据结构，但是在 JDK1.8 以及 1.8 之后采用的`数组+链表+红黑树`的数据结构底部存储数据，当链表的长度（阈值）达到 **8** 的时候，它的链表会转换为红黑树，当红黑树的长度减小到 **6** 的时候，红黑树转换为链表，为什么是 **8** 呢？，因为西方的采用**泊松分布**减少Hash的碰撞次数。
![HashMap的底部数据结构](https://pic.downk.cc/item/5ea0f419c2a9a83be577c94d.png)

## Q2：HashMap中 hash 函数怎么是是实现的?
**回答：** `异或 + 与 运算` 。“模”运算的消耗还是比较大的，能不能找一种更快速，消耗更小的方式，我们来看看JDK1.8采用**位异或**方式代替取模运算。(h ^ (h >>> 16)) hash函数的位干扰。减少hash碰撞。

```
static final int hash(Object key) {
    if (key == null){
        return 0;
    }
     int h;
     h=key.hashCode()；返回散列值也就是hashcode
      // ^ ：按位异或 （相同得0，不同得1）
      // & ：与运算（相同得1，不同得0）
      // >>>:无符号右移，忽略符号位，空位都以0补齐
      //其中n是数组的长度，即Map的数组部分初始化长度
     return  (n-1)&(h ^ (h >>> 16));
}
```
![image.png](https://pic.downk.cc/item/5ea0f45ac2a9a83be577f8ba.png)
**简单来说就是**
          1.高16bt位不变，低16bit位和高16bit位做了一个异或(得到的HASHCODE转化为32位的二进制，前16位和后16位低16bit和高16bit做了一个异或【相同得0，不同得1】)
          2.(n-1)&hash == 最终hash

## Q2：谈一下HashMap的特性？
**回答：**
- HashMap键值对存储数据实现快速存储，key-value允许为null,key值不可重复，若key重复则覆盖。
- 非同步 线程不安全
- 底层是hash表 无序 不保证插入的顺序
## Q3：拉链法导致的链表过深问题为什么不用二叉查找树代替，而选择红黑树？为什么不一直使用红黑树？
**回答：** 之所以选择红黑树是为了解决二叉查找树的缺陷，二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。而红黑树在插入新数据后可能需要通过左旋，右旋、变色这些操作来保持平衡，引入红黑树就是为了查找数据快，解决链表查询深度的问题，我们知道红黑树属于平衡二叉树，但是为了保持“平衡”是需要付出代价的，但是该代价所损耗的资源要比遍历线性链表要少，所以当长度大于8的时候，会使用红黑树，如果链表长度很短的话，根本不需要引入红黑树，引入反而会慢。

## Q4：说说你对红黑树的见解？
![image.png](https://pic.downk.cc/item/5ea0f476c2a9a83be5780deb.png)
- 每个节点非红即黑
- 根节点总是黑色的
- 如果节点是红色的，则它的子节点必须是黑色的（反之不一定）
- 每个叶子节点都是黑色的空节点（NIL节点）
- 从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）

## Q4： 解决hash 碰撞还有那些办法？
开放定址法。

## Q3：HashMap的工作原理？
**回答：** HashMap给予Hashing原理，采用native本地方法调用C++函数写的获取Hash值。我们在通过get()和put()的时候，都要通过哈希算法计算出key的hashcode值，返回的hashcode值用于方便找到Bucket位置来储存或者获得Entry对象，JDK把Entry<K,T>改名为Node<K,T>对象，在put的时候，如果该位置已经有元素了，调用equals方法判断是否相等，相等的话进行替换，不相等的话，新增Node节点放在链表或者红黑树里面。
当获取对象的时候，通过键对象的equals()方法找到正确的键值对，然后返回值对象，HashMap使用链表或者红黑树来解决碰撞问题，当发生碰撞的时候，对象会存储在链表的下一个节点中。
![image.png](https://pic.downk.cc/item/5ea0f496c2a9a83be57824f7.png)

## Q4：如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？
**回答：** 默认的负载因子大小为0.75，也就是说，当一个map填满了75%的bucket时候，和其它集合类(如ArrayList等)一样，将会创建原来HashMap大小的两倍的bucket数组，来重新调整map的大小，并将原来的对象放入新的bucket数组中。这个过程叫作rehashing，因为它调用hash方法找到新的bucket位置。这个值只可能在两个地方，一个是原下标的位置，另一种是在下标为<原下标+原容量>的位置。在1.7版本，多线程下容易发生线程不安全，在重Hash迁移数组的时候，容易出现链表死循环。

## Q5：如何使HashMap变成线程安全的呢?
**回答：**调用工具类**Collections.synchronizedMap(map)**; 或者使用**ConcurrentHashMap**集合类。
>这里不推荐使用HashTable
```
Map mapNew = Collections.synchronizedMap(map);
```
## Q5：重新调整HashMap大小存在什么问题吗？
- 当重新调整HashMap大小的时候，确实存在条件竞争，因为如果两个线程都发现HashMap需要重新调整大小了，它们会同时试着调整大小。在调整大小的过程中，存储在链表中的元素的次序会反过来，因为移动到新的bucket位置的时候，HashMap并不会将元素放在链表的尾部，而是放在头部，这是为了避免尾部遍历(tail traversing)。如果条件竞争发生了，那么就死循环了。(多线程的环境下不使用HashMap）
为什么多线程会导致死循环，它是怎么发生的？
　　HashMap的容量是有限的。当经过多次元素插入，使得HashMap达到一定饱和度时，Key映射位置发生冲突的几率会逐渐提高。这时候，HashMap需要扩展它的长度，也就是  　　进行Resize。1.扩容：创建一个新的Entry空数组，长度是原数组的2倍。2.ReHash：遍历原Entry数组，把所有的Entry重新Hash到新数组。
## Q4：HashMap的bucket数组的数量默认为多少？它的取值范围是多少？
**回答：** HashMap的bucket数组数量的**默认大小为16**，它的要求是必须为2的整数次密。所以不可能为15,21,22······等等。
- 为了数据的均匀分布，减少哈希碰撞。因为确定数组位置是用的位运算，若数据不是2的次幂则会增加哈希碰撞的次数和浪费数组空间。(PS:其实若不考虑效率，求余也可以就不用位运算了也不用长度必需为2的幂次)
- 输入数据若不是2的幂，HashMap通过一通位移运算和或运算得到的肯定是2的幂次数，并且是离那个数最近的数字，**获取大于它的最小2的整数幂**

## Q4：HashMap键值为什么只能为引用类型？
**回答：** 因为HashMap要根据hashcode()和equal()来保证键的唯一。

## Q4：谈一下hashMap中put是如何实现的？
**回答：**
1.计算关于key的hashcode值（与Key.hashCode的高16位做异或运算）
2.如果散列表为空时，调用resize()初始化散列表
3.如果没有发生碰撞，直接添加元素到散列表中去
4.如果发生了碰撞(hashCode值相同)，进行三种判断
    4.1:若key地址相同或者equals后内容相同，则替换旧值
    4.2:如果是红黑树结构，就调用树的插入方法
    4.3：链表结构，循环遍历直到链表中某个节点为空，尾插法进行插入，插入之后判断链表个数是否到达变成红黑树的阙值8；也可以遍历到有节点与插入元素的哈希值和内容相同，进行覆盖。
5.如果桶满了大于阀值，则resize进行扩容

## Q4：谈一下hashMap中get是如何实现的？
**回答：** 对key的hashCode进行hashing，与运算计算下标获取bucket位置，如果在桶的首位上就可以找到就直接返回，否则在树中找或者链表中遍历找，如果有hash冲突，则利用equals方法去遍历链表查找节点。

## Q4：谈一下HashMap中hash函数是怎么实现的？还有哪些hash函数的实现方式？
**回答：**对key的hashCode做hash操作，与高16位做异或运算还有平方取中法，除留余数法，伪随机数法

## Q4：为什么不直接将key作为哈希值而是与高16位做异或运算？
**回答：**因为数组位置的确定用的是与运算，仅仅最后四位有效，设计者将key的哈希值与高16为做异或运算使得在做&运算确定数组的插入位置时，此时的低位实际是高位与低位的结合，增加了随机性，减少了哈希碰撞的次数。

## Q4：平时在使用HashMap时一般使用什么类型的元素作为Key？
**回答：**选择Integer，String这种不可变的类型，像对String的一切操作都是新建一个String对象，对新的对象进行拼接分割等，这些类已经很规范的覆写了hashCode()以及equals()方法。作为不可变类天生是线程安全的，

## Q4：传统hashMap的缺点(为什么引入红黑树？)：
**回答：**JDK 1.8 以前 HashMap 的实现是 数组+链表，即使哈希函数取得再好，也很难达到元素百分百均匀分布。当 HashMap 中有大量的元素都存放到同一个桶中时，这个桶下有一条长长的链表，这个时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，完全失去了它的优势。针对这种情况，JDK 1.8 中引入了 红黑树（查找时间复杂度为 O(logn)）来优化这个问题。


## Q4：请解释一下HashMap的参数loadFactor，它的作用是什么？
**回答：**loadFactor表示HashMap的拥挤程度，影响hash操作到同一个数组位置的概率。默认loadFactor等于0.75，当HashMap里面容纳的元素已经达到HashMap数组长度的75%时，表示HashMap太挤了，需要扩容，在HashMap的构造器中可以定制loadFactor。

## Q4：HashMap和HashTable的区别
**回答：**
相同点：都是存储key-value键值对的
不同点：
1. HashMap允许Key-value为null，hashTable不允许；
2. hashMap没有考虑同步，是线程不安全的。hashTable是线程安全的，给api套上了一层synchronized修饰;
3. HashMap继承于AbstractMap类，hashTable继承与Dictionary类。
4. 迭代器(Iterator)。HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException。
5. 容量的初始值和增加方式都不一样：HashMap默认的容量大小是16；增加容量时，每次将容量变为"原始容量x2"。Hashtable默认的容量大小是11；增加容量时，每次将容量变为"原始容量x2 + 1"；
6. 添加key-value时的hash值算法不同：HashMap添加元素时，是使用自定义的哈希算法。Hashtable没有自定义哈希算法，而直接采用的key的hashCode()。

## Q4：如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？
**回答：**超过阙值会进行扩容操作，概括的讲就是扩容后的数组大小是原数组的2倍，将原来的元素重新hashing放入到新的散列表中去。
## Q4：谈一下当两个对象的hashCode相等时会怎么样？
**回答：**会产生哈希碰撞，若key值相同则替换旧值，不然链接到链表后面，链表长度超过阙值8就转为红黑树存储

## Q4：如果两个键的hashcode相同，你如何获取值对象？
**回答：**HashCode相同，通过equals比较内容获取值对象