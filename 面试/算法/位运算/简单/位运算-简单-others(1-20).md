**题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 位运算-简单-others(1-20)

## 1.[[剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)]

**描述**

请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

**示例**

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2:**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 2:**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

**题解**

**Java**

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for(int num : nums){
            ans ^= num;
        }
        return ans;
    }
}
```

## 2.[[剑指 Offer 39. 数组中出现次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)]

**描述**

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例**

**示例 1：**

```
输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2
```

**题解**

**Java**

```java
// 摩尔投票法
class Solution {
    public int majorityElement(int[] nums) {
        int ans = 0, count = 0;
        for(int num : nums){
            if(count == 0){
                ans = num;
                count++;
            }else{
                count += ans == num ? 1 : -1;
            }
        }
        return ans;
    }
}
// 排序法
class Solution {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length / 2];
    }
}
```

## 3.[[面试题 05.01. 插入](https://leetcode-cn.com/problems/insert-into-bits-lcci/)]

**描述**

给定两个整型数字 N 与 M，以及表示比特位置的 i 与 j（i <= j，且从 0 位开始计算）。

编写一种方法，使 M 对应的二进制数字插入 N 对应的二进制数字的第 i ~ j 位区域，不足之处用 0 补齐。具体插入过程如图所示。

题目保证从 `i` 位到 `j` 位足以容纳 `M`， 例如： `M = 10011`，则 `i～j` 区域至少可容纳 5 位。

**示例**

**示例 1：**

```
输入：N = 1024(10000000000), M = 19(10011), i = 2, j = 6
输出：N = 1100(10001001100)
```

**示例 2：**

```
输入： N = 0, M = 31(11111), i = 0, j = 4
输出：N = 31(11111)
```

**题解**

**Java**

```java
class Solution {
    public int insertBits(int N, int M, int i, int j) {
        int[] num_2 = new int[32];
        // 先转换为2进制, 存储到数组中
        for(int left=0; left<32; left++){
            num_2[left] = ((N >> left) & 1);
        }
        int x = 0;
        // 将M转为2进制, 替换对应的位置
        for(int left=i; left<j+1; left++){
            num_2[left] = ((M >> x++) & 1); 
        }
        int ans = 0;
        x = 1;
        // 转为10进制
        for(int left=0; left<32; left++){
            ans += num_2[left] == 1 ? x : 0;
            x *= 2;
        }
        return ans;
    }
}
```

## 4.[[面试题 05.03. 翻转数位](https://leetcode-cn.com/problems/reverse-bits-lcci/)]

**描述**

给定一个32位整数 `num`，你可以将一个数位从0变为1。请编写一个程序，找出你能够获得的最长的一串1的长度。

**示例**

**示例 1：**

```
输入: num = 1775(110111011112)
输出: 8
```

**示例 2：**

```
输入: num = 7(01112)
输出: 4
```

**题解**

**Java**

```java
class Solution {
    public int reverseBits(int num) {
        // 转换为2进制数组
        int[] num_2 = new int[32];
        for(int i=0; i<32; i++){
            num_2[i] = 1 & (num >> i);
        }
        // 记录最终长度
        int count = 0;
        // 0前面的长度
        int pre = 0;
        // 0后面的长度
        int after = 0;
        // 是否遇到0
        boolean flag = false;
        // 遍历
        for(int i=0; i<32; i++){
            // 如果等于1
            if(num_2[i] == 1){
                // 并且没有遇到0, 0前面的长度+1
                if(!flag){
                    pre++;
                // 遇到了0, 0后面的长度+1
                }else{
                    after++;
                }
            // 如果等于0
            }else{
                // 之前没有遇到0
                if(!flag){
                    flag = true;
                // 之前遇到过0
                }else{
                    // 计算当前一共多少1
                    count = Math.max(count, pre + 1 + after);
                    // 0前面的长度 = 后面的
                    pre = after;
                    // 后面的变成0
                    after = 0;
                }
            }
        }
        // 最后再判断一边
        if(flag){
            count = Math.max(count, pre + 1 + after);
        }else{
            count = Math.max(count, pre + after);
        }
        return count;
    }
}
```

## 5.[[面试题 05.06. 整数转换](https://leetcode-cn.com/problems/convert-integer-lcci/)]

**描述**

整数转换。编写一个函数，确定需要改变几个位才能将整数A转成整数B。

**示例**

**示例 1：**

```
输入：A = 29 （或者0b11101）, B = 15（或者0b01111）
输出：2
```

**示例 2：**

```
输入：A = 1，B = 2
输出：2
```

**题解**

**Java**

```java
class Solution {
    public int convertInteger(int A, int B) {
        int count = 0;
        for(int i=0; i<32; i++){
            if((1 & (A >> i)) != (1 & (B >> i))){
                count++;
            }
        }
        return count;
    }
}
```

## 6.[[面试题 05.07. 配对交换](https://leetcode-cn.com/problems/exchange-lcci/)]

**描述**

配对交换。编写程序，交换某个整数的奇数位和偶数位，尽量使用较少的指令（也就是说，位0与位1交换，位2与位3交换，以此类推）。

**示例**

**示例 1：**

```
输入：num = 2（或者0b10）
输出 1 (或者 0b01)
```

**示例 2：**

```
输入：num = 3
输出：3
```

**题解**

**Java**

```java
class Solution {
    public int exchangeBits(int num) {
        // 提取出2进制奇数位的数字
        int odd = num & 0x55555555;
        // 提取出2进制偶数位的数字
        int even = num & 0xaaaaaaaa;
        // 奇数左移
        odd <<= 1;
        // 偶数右移
        even >>= 1;
        // 最后或运算, 组合成一个数
        return odd | even;
    }
}
```

