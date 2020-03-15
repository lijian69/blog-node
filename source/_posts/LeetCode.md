---
title: LeetCode 编程进阶  
top: true 
categories: 算法   
tags:
  - LeetCode
  - 算法
  - java
---
LeetCode收录了许多互联网公司的算法题目，被称为刷题神器。如果是为了面试，刷Leetcode还是很重要的。一是提高对简单题的熟悉程度，有一些听起来很轻松的题目，比如旋转矩阵，写过一次和没写过的临场表现可能会有很大的差距；二是可以见识一些大家都知道的奇技淫巧，有一些大公司就是喜欢考这种看似很巧妙，但是Leetcode上有原题的套路。

## LeetCode - 精选 TOP 面试

以下是 LeetCode 的精选 TOP 面试的题，本节汇总 1 ~ n 的向下去编写。持续更新.......

> https://leetcode-cn.com/problemset/top/

###  1、两数之和

####  (1) 试题详情
给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
#### (2) 试题实例

----
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
----

#### (3) 试题解决方案
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int [] result = new int[2];
        for (int i = 0; i < nums.length; i++) {
            int f = target - nums[i];
            for (int j = i+1; j < nums.length; j++) {
                if (nums[j] == f){
                    result[0] = i;
                    result[1] = j;
                }
            }
        }
        return result;
    }
}
```

### 2、反转链表 - 206

#### (1) 试题详情

`跳转到LeetCode` [LeetCode-206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

给定一个链表，请你反转一个单链表。

#### (2) 试题实例

---

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

---

#### (3) 试题解决方案

```java
/**
 * 链表反转
 *
 * @author jian.li
 * @date 2020年 03月06日 15:50:53
 */
public class ReverseList {

    public static void main(String[] args) {
        ListNode listNode = new ListNode(2);
        listNode.next = new ListNode(1);
        listNode.next.next = new ListNode(3);
        listNode.next.next.next = new ListNode(4);

        ListNode newList = reverseList(listNode);
        newList.toListString();

    }

    public static ListNode reverseList(ListNode head) {
        ListNode pre = null;
        ListNode curr = head;
        while(curr != null){
            ListNode next =  curr.next;
            curr.next = pre;
            pre = curr;
            curr = next;
        }
        return pre;
    }

}

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }


    public void toListString() {
        System.out.print(val + " -> ");
        while (next != null){
            System.out.print(next.val + " -> ");
            next = next.next;
        }
        System.out.print("null");
    }
}
```

###  3、用队列实现栈 - 225

#### (1) 试题详情 

`跳转到LeetCode` [225. 用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

使用队列实现栈的下列操作：

- push(x) -- 元素 x 入栈
- pop() -- 移除栈顶元素
- top() -- 获取栈顶元素
- empty() -- 返回栈是否为空

**注意**

- 你只能使用队列的基本操作-- 也就是 push to back, peek/pop from front, size, 和 is empty 这些操作是合法的。
- 你所使用的语言也许不支持队列。 你可以使用 list 或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
- 你可以假设所有操作都是有效的（例如, 对一个空的栈不会调用 pop 或者 top 操作）。



#### (2) 解决办法

```java
public class MyStatckDemo{
    public static void main(String[] args) {
        MyStack myStack = new MyStack();
        myStack.push(2);
        myStack.push(3);
        myStack.push(1);
        System.out.println(myStack.pop());
        System.out.println(myStack.top());
    }
}
class MyStack {

    private List<Integer> list ;
    private int size = 0;
    /** Initialize your data structure here. */
    public MyStack() {
        list = new ArrayList();
    }

    /** Push element x onto stack. */
    public void push(int x) {
        list.add(x);
        size ++;
    }

    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        if (size != 0){
            Integer remove = list.remove(size - 1);
            size --;
            return remove;
        }
        return -1;
    }

    /** Get the top element. */
    public int top() {
        if (size != 0){
            Integer value = list.get(size - 1);
            return value;
        }
        return -1;
    }

    /** Returns whether the stack is empty. */
    public boolean empty() {
        if (size == 0){
            return true;
        }
        return false;
    }
}
```





