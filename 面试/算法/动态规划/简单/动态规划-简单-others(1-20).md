**题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 动态规划-简单-others(1-20)

## 1.[[剑指 Offer 42. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)]

**描述**

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。

**示例**

**示例 1：**

```
输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**提示：**

- `1 <= arr.length <= 10^5`
- `-100 <= arr[i] <= 100`

**题解**

**Java**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        for(int i=1; i<nums.length; i++){
            if(nums[i-1] > 0){
                nums[i] = nums[i] + nums[i-1];
            }
            max = Math.max(nums[i], max);
        }
        return max;
    }
}
```

## 2.[[面试题 08.01. 三步问题](https://leetcode-cn.com/problems/three-steps-problem-lcci/)]

**描述**

三步问题。有个小孩正在上楼梯，楼梯有n阶台阶，小孩一次可以上1阶、2阶或3阶。实现一种方法，计算小孩有多少种上楼梯的方式。结果可能很大，你需要对结果模1000000007。

**示例**

**示例 1：**

```
 输入：n = 3 
 输出：4
 说明: 有四种走法
```

**示例 1：**

```
输入：n = 5
输出：13
```

**提示：**

- n范围在[1, 1000000]之间

**题解**

**Java**

```java
class Solution {
    /* 找规律
    	n    = 1 2 3 4 5  6
    	ans  = 1 2 4 7 13 24
    	可观察出, 从n=4开始, 对应的结果为前三项之和
    */
    public int waysToStep(int n) {
        if(n <= 2) return n;
        if(n == 3) return 4;
        int one = 1;
        int two = 2;
        int three = 4;
        for(int i=4; i<=n; i++){
            int tmp = one;
            one = two;
            two = three;
            // 由于每两项相加都有可能超出范围, 所以每两项相加都要取模
            three = ((tmp + one) % 1000000007 + two) % 1000000007;
        }
        return three;
    }
}
```

## 3.[[面试题 16.17. 连续数列](https://leetcode-cn.com/problems/contiguous-sequence-lcci/)]

**描述**

给定一个整数数组，找出总和最大的连续数列，并返回总和。

**示例**

**示例 1：**

```
输入： [-2,1,-3,4,-1,2,1,-5,4]
输出： 6
解释： 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**题解**

**Java**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        for(int i=1; i<nums.length; i++){
            if(nums[i-1] > 0){
                nums[i] = nums[i] + nums[i-1];
            }
            max = Math.max(nums[i], max);
        }
        return max;
    }
}
```

## 4.[[面试题 17.16. 按摩师](https://leetcode-cn.com/problems/the-masseuse-lcci/)]

**描述**

一个有名的按摩师会收到源源不断的预约请求，每个预约都可以选择接或不接。在每次预约服务之间要有休息时间，因此她不能接受相邻的预约。给定一个预约请求序列，替按摩师找到最优的预约集合（总预约时间最长），返回总的分钟数。

注意：本题相对原题稍作改动

**示例**

**示例 1：**

```
输入： [1,2,3,1]
输出： 4
解释： 选择 1 号预约和 3 号预约，总时长 = 1 + 3 = 4。
```

**示例 2：**

```
输入： [2,7,9,3,1]
输出： 12
解释： 选择 1 号预约、 3 号预约和 5 号预约，总时长 = 2 + 9 + 1 = 12。
```

**示例 3：**

```
输入： [2,1,4,5,3,1,1,3]
输出： 12
解释： 选择 1 号预约、 3 号预约、 5 号预约和 8 号预约，总时长 = 2 + 4 + 3 + 3 = 12。
```

**题解**

**Java**

```java
class Solution {
    public int massage(int[] nums) {
        if(nums.length == 0) return 0;
        if(nums.length == 1) return nums[0];
        int one = nums[0];
        int two = Math.max(nums[1], one);
        for(int i=2; i<nums.length; i++){
            int tmp = one;
            one = two;
            two = Math.max(two, tmp + nums[i]);
        }
        return Math.max(one, two);
    }
}
```

