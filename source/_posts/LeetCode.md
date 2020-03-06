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

​		你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

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

