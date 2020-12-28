**题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 栈-简单(1-20)

## 1.[[剑指 Offer 40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)]

**描述**

输入整数数组 `arr` ，找出其中最小的 `k` 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

**示例**

**示例 1：**

```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

**示例 2：**

```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```

**题解**

**题解**

**Java**

优先队列

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k == 0) return new int[0];
        PriorityQueue<Integer> queue = new PriorityQueue((o1, o2) -> {
            return (int)o2 - (int)o1; 
        });
        for(int num : arr){
            if(queue.size() < k){
                queue.add(num);
            }else if(queue.peek() > num){
                queue.poll();
                queue.add(num);
            }
        }
        int[] ans = new int[k];
        int index = 0;
        for(int num : queue){
            ans[index++] = num;
        }
        return ans;
    }
}
```

排序

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        Arrays.sort(arr);
        int[] ans = new int[k];
        for(int i=0; i<k; i++){
            ans[i] = arr[i];
        }
        return ans;
    }
}
```

