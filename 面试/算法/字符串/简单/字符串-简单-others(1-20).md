**题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 字符串-简单-others(1-20)

## 1.[[剑指 Offer 58 - I. 翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)]

**描述**

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

**示例**

**示例 1：**

```
输入: "the sky is blue"
输出: "blue is sky the"
```

**示例 2：**

```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

**示例 3：**

```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

**提示：**

- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

**题解**

**Java**

```java
class Solution {
    public String reverseWords(String s) {
        // 去除前后空格
        s = s.trim();
        StringBuilder sb = new StringBuilder();
        char[] chars = s.toCharArray();
        // 截取的字符串的末尾位置
        int end = chars.length;
        // 倒叙遍历
        for(int i=chars.length-1; i>=0; i--){
            // 如果是第一个位置, 起始位置i不需要+1截取, 插入sb
            if(i == 0){
                sb.append(s.substring(i, end));
                // 更新end的位置
                end = i;
            }else if(chars[i] == ' '){
                // 遇到空格, 并且上一个字符不是空格, 起始位置i+1, 截取字符串, 插入sb, 插入空格
                if(chars[i+1] != ' '){
                    sb.append(s.substring(i+1, end)).append(' ');
                }
                // 更新end位置
                end = i;
            }
        }
        return sb.toString();
    }
}
```

## 2.[[剑指 Offer 58 - II. 左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)]

**描述**

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

**示例**

**示例 1：**

```
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

**示例 2：**

```
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"
```

**提示：**

- `1 <= k < s.length <= 10000`

**题解**

**Java**

```java
class Solution {
    // 截取后拼接
    public String reverseLeftWords(String s, int n) {
        StringBuilder sb = new StringBuilder();
        sb.append(s.substring(n)).append(s.substring(0,n));
        return sb.toString();
    }
}
```

## 3.[[面试题 01.02. 判定是否互为字符重排](https://leetcode-cn.com/problems/check-permutation-lcci/)]

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
        if(s1.length() != s2.length()) return false;
        int[] bucket = new int[26];
        for(char c : s1.toCharArray()){
            bucket[c - 'a']++;
        }
        for(char c : s2.toCharArray()){
            if(bucket[c - 'a'] == 0){
                return false;
            }
            bucket[c - 'a']--;
        }
        return true;
    }
}
```

## 4.[[面试题 01.03. URL化](https://leetcode-cn.com/problems/string-to-url-lcci/)]

**描述**

URL化。编写一种方法，将字符串中的空格全部替换为%20。假定该字符串尾部有足够的空间存放新增字符，并且知道字符串的“真实”长度。（注：用Java实现的话，请使用字符数组实现，以便直接在数组上操作。）

**示例**

**示例 1：**

```
输入："Mr John Smith    ", 13
输出："Mr%20John%20Smith"
```

**示例 2：**

```
输入："               ", 5
输出："%20%20%20%20%20"
```

**提示：**

- 字符串长度在 [0, 500000] 范围内。

**题解**

**Java**

```java
class Solution {
    public String replaceSpaces(String S, int length) {
        char[] chars = S.toCharArray();
        // 声明三倍长度的数组, 防止全是空格的情况
        char[] ans = new char[length * 3];
        int start = 0;
        for(int i=0; i<length; i++){
            if(chars[i] == ' '){
                ans[start++] = '%';
                ans[start++] = '2';
                ans[start++] = '0';
            }else{
                ans[start++] = chars[i];
            }
        }
        // 使用数组的有效部分创建字符串
        return new String(ans, 0, start);
    }
}
```

## 5.[[面试题 01.04. 回文排列](https://leetcode-cn.com/problems/palindrome-permutation-lcci/)]

**描述**

给定一个字符串，编写一个函数判定其是否为某个回文串的排列之一。

回文串是指正反两个方向都一样的单词或短语。排列是指字母的重新排列。

回文串不一定是字典当中的单词。

**示例**

**示例 1：**

```
输入："tactcoa"
输出：true（排列有"tacocat"、"atcocta"，等等）
```

**题解**

**Java**

```java
class Solution {
    // map记录出现的字符数量
    // 奇数数量的字符超过1个, 肯定不是回文排列
    public boolean canPermutePalindrome(String s) {
        Map<Character, Integer> map = new HashMap();
        for(char c : s.toCharArray()){
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        int oddCount = 0;
        for(int num : map.values()){
            if(num % 2 == 1) oddCount++;
        }
        return oddCount < 2;
    }
}
```

## 6.[[面试题 01.06. 字符串压缩](https://leetcode-cn.com/problems/compress-string-lcci/)]

**描述**

字符串压缩。利用字符重复出现的次数，编写一种方法，实现基本的字符串压缩功能。比如，字符串aabcccccaaa会变为a2b1c5a3。若“压缩”后的字符串没有变短，则返回原先的字符串。你可以假设字符串中只包含大小写英文字母（a至z）。

**示例**

**示例 1：**

```
 输入："aabcccccaaa"
 输出："a2b1c5a3"
```

**示例 2：**

```
 输入："abbccd"
 输出："abbccd"
 解释："abbccd"压缩后为"a1b2c2d1"，比原字符串长度更长。
```

**提示：**

1. 字符串长度在[0, 50000]范围内。

**题解**

**Java**

```java
class Solution {
    // 快慢指针
    public String compressString(String S) {
        if(S.length() == 0) return S;
        char[] chars = S.toCharArray();
        StringBuilder sb = new StringBuilder();
        int slow = 0, fast = 0;
        while(fast <= chars.length){
            if(fast == chars.length){
                sb.append(chars[slow]).append(fast-slow);
            }else if(chars[fast] != chars[slow]){
                sb.append(chars[slow]).append(fast-slow);
                slow = fast;
            }
            fast++;
        }
        return sb.length() < chars.length ? sb.toString() : S;
    }
}
```

## 7.[[面试题 01.09. 字符串轮转](https://leetcode-cn.com/problems/string-rotation-lcci/)]

**描述**

字符串轮转。给定两个字符串`s1`和`s2`，请编写代码检查`s2`是否为`s1`旋转而成（比如，`waterbottle`是`erbottlewat`旋转后的字符串）。

**示例**

**示例 1：**

```
 输入：s1 = "waterbottle", s2 = "erbottlewat"
 输出：True
```

**示例 2：**

```
 输入：s1 = "aa", s2 = "aba"
 输出：False
```

**提示：**

1. 字符串长度在[0, 100000]范围内。

**题解**

**Java**

```java
class Solution {
    // s1+s1, 如果包含s2, 并且 s1的长度和s2相等, 那么, s1一定使用s2轮转的到的
    public boolean isFlipedString(String s1, String s2) {
        if(s1.length() != s2.length()) return false;
        return (s1+s1).indexOf(s2) != -1;
    }
}
```

