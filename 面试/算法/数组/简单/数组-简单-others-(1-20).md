题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 数组-简单(1-20)

## 1.[[剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)]

**描述**

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例**

**示例 1：**

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

**提示：**

- 2 <= n <= 100000

**题解**

**Java**

```java
class Solution {
    // 遍历数组, 每个数字放置到与数字索引的位置上, 每次查询索引对应的数字与当前的数字是否相同
    public int findRepeatNumber(int[] nums) {
        for(int i=0; i<nums.length; i++){
            while(nums[i] != i){
                if(nums[i] == nums[nums[i]]){
                    return nums[i];
                }
                int tmp = nums[i];
                nums[i] = nums[tmp];
                nums[tmp] = tmp;
            }
        }
        return -1;
    }
}
```

## 2.[[剑指 Offer 29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)]

**描述**

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

**示例**

**示例 1：**

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例 2：**

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**提示：**

- `0 <= matrix.length <= 100`
- `0 <= matrix[i].length <= 100`

**题解**

**Java**

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length == 0 || matrix[0].length == 0){
            return new int[0];
        }
        int len = matrix.length, clen = matrix[0].length;
        int[] ans = new int[len * clen];
        int start = 0;
        // 创建四个变量, 分别表示上下左右四个边界
        int left = 0, right = clen - 1, top = 0, bottom = len - 1;
        while(left <= right && top <= bottom){
            // 遍历上边
            for(int i = left; i <= right; i++){
                ans[start++] = matrix[top][i];
            }
            // 遍历右边
            for(int i = top + 1; i <= bottom; i++){
                ans[start++] = matrix[i][right];
            }
            // 如果只剩最中间的一条, 上面两个循环已经完成遍历
            if(left < right && top < bottom){
                // 遍历下边
                for(int i = right - 1; i >= left; i--){
                    ans[start++] = matrix[bottom][i];
                }
                // 遍历左边
                for(int i = bottom - 1; i > top; i--){
                    ans[start++] = matrix[i][left];
                }
            }
            // 收窄四边边界
            left++;
            right--;
            top++;
            bottom--;
        }
        return ans;
    }
}
```

## 3.[[剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)]

**描述**

统计一个数字在排序数组中出现的次数。

**示例**

**示例 1：**

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**示例 2：**

```
输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
```

**提示：**

- 0 <= 数组长度 <= 50000

**题解**

**Java**

```java
class Solution {
   	// 使用二分查找, 找到后目标数字后, 向两边扩散查找
    public int search(int[] nums, int target) {
        int count = 0;
        int left = 0, right = nums.length - 1; 
        while(left <= right){
            int half = (left + right) / 2;
            if(target > nums[half]){
                left = half + 1;
            }else if(target < nums[half]){
                right = half - 1;
            }else{
                count++;
                for(int i = half - 1; i >= 0; i--){
                    if(nums[i] != target) break;
                    count++;
                }
                for(int i = half + 1; i < nums.length; i++){
                    if(nums[i] != target) break;
                    count++;
                }
                break;
            }
        }
        return count;
    }
}
```

## 4.[[剑指 Offer 53 - II. 0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)]

**描述**

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

**示例**

**示例 1：**

```
输入: [0,1,3]
输出: 2
```

**示例 2：**

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

**提示：**

- 0 <= 数组长度 <= 50000

**题解**

**Java**

```java
class Solution {
    // 使用异或
    public int missingNumber(int[] nums) {
        int len = nums.length;
        for(int i = 0; i < nums.length; i++){
            len ^= nums[i] ^ i;
        }
        return len;
    }
}
```

```java
class Solution {
    // 二分法
    public int missingNumber(int[] nums) {
        int left = 0, right = nums.length-1;
        // 不断缩小范围
        while(left <= right){
            int half = left + (right - left) / 2;
            if(nums[half] == half){
                left = half + 1;
            }else {
                right = half - 1;
            }
        }
        return left;
    }
}
```

## 5.[[面试题 01.01. 判定字符是否唯一](https://leetcode-cn.com/problems/is-unique-lcci/)]

**描述**

实现一个算法，确定一个字符串 `s` 的所有字符是否全都不同。

**示例**

**示例 1：**

```
输入: s = "leetcode"
输出: false 
```

**示例 2：**

```
输入: s = "abc"
输出: true
```

**提示：**

- `0 <= len(s) <= 100`
- 如果你不使用额外的数据结构，会很加分。

**题解**

**Java**

```java
class Solution {
    public boolean isUnique(String astr) {
        for(int i=0; i<astr.length(); i++){
            for(int j=i+1; j<astr.length(); j++){
                if(astr.charAt(i) == astr.charAt(j)){
                    return false;
                }
            }
        }
        return true;
    }
}
```

## 6.[[面试题 01.02. 判定是否互为字符重排](https://leetcode-cn.com/problems/check-permutation-lcci/)]

**描述**

给定两个字符串 `s1` 和 `s2`，请编写一个程序，确定其中一个字符串的字符重新排列后，能否变成另一个字符串。

**示例**

**示例 1：**

```
输入: s1 = "abc", s2 = "bca"
输出: true 
```

**示例 2：**

```
输入: s1 = "abc", s2 = "bad"
输出: false
```

**提示：**

- `0 <= len(s1) <= 100`
- `0 <= len(s2) <= 100`

**题解**

**Java**

```java
class Solution {
    // 桶排序
    public boolean CheckPermutation(String s1, String s2) {
        int[] bucket = new int[26];
        for(int i=0; i<s1.length(); i++){
            bucket[s1.charAt(i) - 'a']++;
        }
        for(int i=0; i<s2.length(); i++){
            if(bucket[s2.charAt(i) - 'a'] == 0){
                return false;
            }else{
                bucket[s2.charAt(i) - 'a']--;
            }
        }
        return true;
    }
}
```

## 7.[[面试题 08.03. 魔术索引](https://leetcode-cn.com/problems/magic-index-lcci/)]

**描述**

魔术索引。 在数组A[0...n-1]中，有所谓的魔术索引，满足条件A[i] = i。给定一个有序整数数组，编写一种方法找出魔术索引，若有的话，在数组A中找出一个魔术索引，如果没有，则返回-1。若有多个魔术索引，返回索引值最小的一个。

**示例**

**示例 1：**

```
 输入：nums = [0, 2, 3, 4, 5]
 输出：0
 说明: 0下标的元素为0
```

**示例 2：**

```
 输入：nums = [1, 1, 1]
 输出：1
```

**提示：**

- nums长度在[1, 1000000]之间
- 此题为原书中的 Follow-up，即数组中可能包含重复元素的版本

**题解**

**Java**

```java
class Solution {
    // 间隔跳跃
    // 每次比较当前数和索引, 不相等时, 比较当前数和下一个索引哪个比较大, 指针跳到较大的数
    // 因为, 目标数一定大于当前数, 和当前数对应的正确的索引
    public int findMagicIndex(int[] nums) {
        for(int i=0; i<nums.length;){
            if(nums[i] == i){
                return i;
            }
            i = Math.max(i+1, nums[i]);
        }
        return -1;
    }
}
```

## 8.[[面试题 10.01. 合并排序的数组](https://leetcode-cn.com/problems/sorted-merge-lcci/)]

**描述**

给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。

初始化 A 和 B 的元素数量分别为 m 和 n。

**示例**

**示例 1：**

```
输入:
A = [1,2,3,0,0,0], m = 3
B = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]
```

**提示：**

- `A.length == n + m`

**题解**

**Java**

```java
class Solution {
    public void merge(int[] A, int m, int[] B, int n) {
        int end = m + n - 1;
        int i = m - 1;
        int j = n - 1;
        while(i >=0 && j >= 0){
            A[end--] = A[i] > B[j] ? A[i--] : B[j--];
        }
        if(j >= 0){
            System.arraycopy(B, 0, A, 0, j + 1);
        }
    }
}
```

## 9.[[面试题 16.15. 珠玑妙算](https://leetcode-cn.com/problems/master-mind-lcci/)]

**描述**

珠玑妙算游戏（the game of master mind）的玩法如下。

计算机有4个槽，每个槽放一个球，颜色可能是红色（R）、黄色（Y）、绿色（G）或蓝色（B）。例如，计算机可能有RGGB 4种（槽1为红色，槽2、3为绿色，槽4为蓝色）。作为用户，你试图猜出颜色组合。打个比方，你可能会猜YRGB。要是猜对某个槽的颜色，则算一次“猜中”；要是只猜对颜色但槽位猜错了，则算一次“伪猜中”。注意，“猜中”不能算入“伪猜中”。

给定一种颜色组合solution和一个猜测guess，编写一个方法，返回猜中和伪猜中的次数answer，其中answer[0]为猜中的次数，answer[1]为伪猜中的次数。

**示例**

**示例 1：**

```
输入： solution="RGBY",guess="GGRR"
输出： [1,1]
解释： 猜中1次，伪猜中1次。
```

**提示：**

- `len(solution) = len(guess) = 4`
- `solution`和`guess`仅包含`"R"`,`"G"`,`"B"`,`"Y"`这4种字符

**题解**

**Java**

```java
class Solution {
    // 伪猜中次数 = 总的猜中次数 - 真猜中次数
    public int[] masterMind(String solution, String guess) {
        int[] ans = new int[2];
        int[] pos = new int[4];
        int trueCount = 0;
        int allCount = 0;
        for(int i = 0; i < guess.length(); i++){
            // 计算中猜中次数
            for(int j = 0; j < solution.length(); j++){
                if(guess.charAt(i) == solution.charAt(j) && pos[j] != 1){
                    allCount++;
                    pos[j] = 1;
                    break;
                }
            }
            // 真猜中次数
            for(int j = 0; j < solution.length(); j++){
                if(guess.charAt(i) == solution.charAt(j) && i == j){
                    trueCount++;
                    break;
                }
            }
        }
        ans[0] = trueCount;
        ans[1] = allCount - trueCount;
        return ans;
    }
}
```

## 10.[[面试题 16.17. 连续数列](https://leetcode-cn.com/problems/contiguous-sequence-lcci/)]

**描述**

给定一个整数数组，找出总和最大的连续数列，并返回总和

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
        int sum = nums[0];
        int tmp = sum;
        // tmp为负数的时候就舍弃掉,  直接等于下一个数, 因为一个负数+另一个数, 一定小于另一个数
        // 为正数的时候, 再累加
        for(int i = 1; i < nums.length; i++){
            if(tmp < 0){
                tmp = nums[i];
            }else{
                tmp += nums[i];
            }
            sum = Math.max(tmp, sum);
        }
        return sum;
    }
}
```

## 11.[[面试题 17.04. 消失的数字](https://leetcode-cn.com/problems/missing-number-lcci/)]

**描述**

数组`nums`包含从`0`到`n`的所有整数，但其中缺了一个。请编写代码找出那个缺失的整数。你有办法在O(n)时间内完成吗？

**注意：**本题相对书上原题稍作改动

**示例**

**示例 1：**

```
输入：[3,0,1]
输出：2
```

**示例 2：**

```
输入：[9,6,4,2,3,5,7,0,1]
输出：8
```

**题解**

**Java**

```java
class Solution {
    // 异或
    public int missingNumber(int[] nums) {
        int ans = nums.length;
        for(int i=0; i < nums.length; i++){
            ans ^= i ^ nums[i];
        }
        return ans;
    }
}
```

## 12.[[面试题 17.10. 主要元素](https://leetcode-cn.com/problems/find-majority-element-lcci/)]

**描述**

数组中占比超过一半的元素称之为主要元素。给定一个**整数**数组，找到它的主要元素。若没有，返回-1。

**示例**

**示例 1：**

```
输入：[1,2,5,9,5,9,5,5,5]
输出：5
```

**示例 2：**

```
输入：[3,2]
输出：-1
```

**示例 2：**

```
输入：[2,2,1,1,1,2,2]
输出：2
```

**说明：**
你有办法在时间复杂度为 O(N)，空间复杂度为 O(1) 内完成吗？

**题解**

**Java**

```java
class Solution {
    // 使用摩尔投票算法
    // 相同的数数量+1, 不同的数数量-1, 当数量等于0时, 被投票的数字换成当前比对的数字, 数量记为1
    // 最终得到数就是超过一半的数
    // 该算法的前提是, 一定有超过一半数, 但是该题无法保证, 所以最终得到的数, 要再遍历一遍计算是否超过一半
    public int majorityElement(int[] nums) {
        if(nums.length == 0) return -1;
        int ans = nums[0];
        int count = 1;
        for(int num : nums){
            if(num == ans){
                count++;
            }else{
                count--;
            }
            if(count == 0){
                ans = num;
                count = 1;
            }
        }
        int len = nums.length / 2;
        for(int num : nums){
            if(num == ans) len--;
            if(len == -1) return ans;
        } 
        return -1;
    }
}
```

