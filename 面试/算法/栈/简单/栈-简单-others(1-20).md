**题目来源**

[力扣（LeetCode）](https://leetcode-cn.com/)

[TOC]



# 字符串-简单-others(1-20)

## 1.[[剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)]

**描述**

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

**示例**

**示例 1：**

```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

**示例 2：**

```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

**提示：**

- `1 <= values <= 10000`
- `最多会对 appendTail、deleteHead 进行 10000 次调用`

**题解**

**Java**

```java
class CQueue {

    private Stack<Integer> stack1;
    private Stack<Integer> stack2;

    public CQueue() {
        stack1 = new Stack();
        stack2 = new Stack();
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        if(stack1.size() + stack2.size() == 0) return -1;
        if(!stack2.isEmpty()){
            return stack2.pop();
        }else{
            while(stack1.size() > 1){
                stack2.push(stack1.pop());
            }
            return stack1.pop();
        }
    }
}
```

## 2.[[剑指 Offer 30. 包含min函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)]

**描述**

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

**示例**

**示例 1：**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

**提示：**

- 各函数的调用总次数不超过 20000 次

**题解**

**Java**

```java
class MinStack {
	
    Stack<Integer> stackA, stackB;

    /** initialize your data structure here. */
    public MinStack() {
        stackA = new Stack();
        stackB = new Stack();
    }
    
    // 使用辅助栈, 栈A存储全部数据
    // 栈B从大到小存储数据, 不满足从大到小的数, 不存储, 栈B就存储了, 每一步的最小值
    public void push(int x) {
        stackA.push(x);
        if(stackB.isEmpty() || stackB.peek() >= x){
            stackB.push(x);
        }
    }
    
    public void pop() {
        if(stackA.pop().equals(stackB.peek())){
            stackB.pop();
        }
    }
    
    public int top() {
        return stackA.peek();
    }
    
    public int min() {
        return stackB.peek();
    }
}
```

## 3.[[面试题 03.02. 栈的最小值](https://leetcode-cn.com/problems/min-stack-lcci/)]

**描述**

请设计一个栈，除了常规栈支持的pop与push函数以外，还支持min函数，该函数返回栈元素中的最小值。执行push、pop和min操作的时间复杂度必须为O(1)。

**示例**

**示例 1：**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**题解**

**Java**

```java
class MinStack {

    Stack<Integer> a, b;

    /** initialize your data structure here. */
    public MinStack() {
        a = new Stack();
        b = new Stack();
    }
    
    public void push(int x) {
        a.push(x);
        if(b.isEmpty() || b.peek() >= x){
            b.push(x);
        }
    }
    
    public void pop() {
        if(a.pop().equals(b.peek())){
            b.pop();
        }
    }
    
    public int top() {
        return a.peek();
    }
    
    public int getMin() {
        return b.peek();
    }
}
```

## 4.[[面试题 03.04. 化栈为队](https://leetcode-cn.com/problems/implement-queue-using-stacks-lcci/)]

**描述**

实现一个MyQueue类，该类用两个栈来实现一个队列。

**示例**

**示例 1：**

```
MyQueue queue = new MyQueue();

queue.push(1);
queue.push(2);
queue.peek();  // 返回 1
queue.pop();   // 返回 1
queue.empty(); // 返回 false
```

**说明：**

- 你只能使用标准的栈操作 -- 也就是只有 push to top, peek/pop from top, size 和 is empty 操作是合法的。
- 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
- 假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）。

**题解**

**Java**

```java
class MyQueue {

    private Stack<Integer> stack1, stack2;

    /** Initialize your data structure here. */
    public MyQueue() {
        stack1 = new Stack();
        stack2 = new Stack();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if(!stack2.isEmpty()){
            return stack2.pop();
        }
        while(stack1.size() > 1){
            stack2.push(stack1.pop());
        }
        return stack1.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if(!stack2.isEmpty()){
            return stack2.peek();
        }
        while(!stack1.isEmpty()){
            stack2.push(stack1.pop());
        }
        return stack2.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return stack1.size() + stack2.size() == 0;
    }
}
```

