[TOC]

# 基础

## 数据类型

### 数字类型

```java
// 类型的值可以用下划线进行分割
int a = 100_000_000;
```

## 运算符

### 位运算

```
  A = 0011 1100
  B = 0000 1101
A&B = 0000 1100  都为1时为1
A|B = 0011 1101  有1为1
A^B = 0011 0001  只有一个1时为1
 ~B = 1111 0010  1变0， 0变1
 
 << 左移 0001 变为 0010 相当于*2; so  2<<3 2左移三位 0010 -> 10000 为16
 >> 右移 相当于 /2   
```

## 方法

### 可变参数

参数数量不固定， 可变参数必须为参数列表中的最后一个

```java
public void method(int... params){
    System.out.print(params[0])
}
```

## 数组

### Arrays

```java
int a[] = {1,2,3,4,5,0}
// 打印
System.out.println(Arrays.toString(a));
// 排序
Arrays.sort(a);
// 填充, 全部填充100
Arrays.fill(a, 100)
// 从2-4开始填充   
Arrays.fill(a, 2, 4, 100)
```

### 排序算法

#### 冒泡排序

1.   比较数组中， 每两个相邻的元素，如果第一个数比第二个数大， 我们就交换他们的位置
2.   每一次比较， 都会产生一个出一个最大或者最小的数字
3.   下一轮则可以少一次排序
4.   依次循环， 直到结束

```java
public static int[] sort(int[] array){
    int tmp = 0;
    boolean flag = false;
    for(int i=0; i<array.length-1; i++){
        for(int j=0; j<array.length-i-1; j++){
            if(array[j] < array[j+1]){
                tmp = array[j];
                array[j] = array[j+1];
                array[j+1] = tmp;
                flag = true;
            }
        }
        if (!flag){
            break;
        }
    }
    return array;
}
```

### 稀疏数组

将二维数组压缩， 只存储有效的数据

```java
// 二维数组 11 * 11
0	0	0	0	0	0	0	0	0	0	0	
0	0	1	0	0	0	0	0	0	0	0	
0	0	0	2	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
// 稀疏数组 
// 第一行第一个值为原数组行数， 第二个值为原数组列数， 第三个值为原数组有效值个数
// 从第二行开始， 每一行第一个值为有效值行索引， 第二个值为有效值列索引， 第三个为有效值本身
11	11	2	
1	2	1	
2	3	2	
```



```java
// 构建二维数组
int [][] array1 = new int[11][11];
array1[1][2] = 1;
array1[2][3] = 2;
int sum = 0;

// 求出二维数组中的有效个数
for (int [] ints : array1){
    for (int aInt : ints){
        if(aInt != 0){
            sum++;
        }
        System.out.print(aInt+"\t");
    }
    System.out.println();
}
System.out.println("有效值个数：" + sum);

// 创建稀疏数组
int [][] array2 = new int[sum+1][3];
array2[0][0] = 11;
array2[0][1] = 11;
array2[0][2] = sum;

// 遍历数组， 将非零的值得的位置， 保存到稀疏数组
int count = 0;
for (int i=0; i<array1.length; i++){
    for (int j=0; j<array1[i].length; j++){
        if (array1[i][j] != 0){
            count++;
            array2[count][0] = i;
            array2[count][1] = j;
            array2[count][2] = array1[i][j];
        }
    }
}
// 遍历稀疏数组
System.out.println("稀疏数组");
for (int [] ints : array2){
    for (int aInt : ints){
        if(aInt != 0){
            sum++;
        }
        System.out.print(aInt+"\t");
    }
    System.out.println();
}

// 创建新数组， 用于还原
int[][] array3 = new int[array2[0][0]][array2[0][1]];
for (int i=1; i<array2.length; i++){
    array3[array2[i][0]][array2[i][1]] = array2[i][2];
}
// 遍历还原的数组
for (int [] ints : array3){
    for (int aInt : ints){
        if(aInt != 0){
            sum++;
        }
        System.out.print(aInt+"\t");
    }
    System.out.println();
}
```

## 对象

### 创建对象内存分析

- 加载类
- new Object() 在堆中创建对象
- 在栈中保存， 对象的索引
- 调用对象的方法时， 会从方法区中调用
- 调用类的静态的方法， 会从静态方法区中调用

### 面向对象三大特性

#### 封装

可在set方法中， 校验数据

- 提高程序的安全性
- 隐藏代码的实现细节
- 统一接口
- 提高系统可维护性

#### 继承

#### 多态

- 父类对象子类实例化， 调用静态方法时， 只会调用， 父类的静态方法
- 无法重写
  - static 
  - final
  - private

## 关键字

### static

- **静态代码块**

  ```java
  public class Person{
      // 最先执行, 多次创建对象，也只执行一次
      static{
  		System.out.println("静态代码块");
  	}
      // 第二个执行
      {
  		System.out.println("匿名代码块");
  	}
      // 第三个执行
      public Person(){
  		System.out.println("构造方法");
  	}
  }
  
  ```

- 静态导入包

  ```java
  // 导入方法
  import static java.lang.Math.random
  ```

# 高级

## 内部类

### 内部类

可以调用外部类的私有方法， 和私有属性

```java
public class Outer{
    public class Outer{
    private int id;
    public void out(){
        System.out.println("这是外部类方法");
    }
    
    class Inner{
        public void in(){
            System.out.println("这是内部类方法");
        }
    }
}
```

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.in();
```

### 静态内部类

只能调用外部类的静态属性和方法， 因为静态类在初始化时， 还没有非静态属性和方法

```java
public class Outer{
    static class Inner{
        public void in(){
            System.out.println("这是内部类方法");
        }
    }
}
```

```java
Outer.Inner inner = new Outer.Inner();
inner.in();
```

### 局部内部类

定义在方法中的类， 不常用

```java
public class Outer{
    public void method(){
        class Inner{
            public void in(){
                System.out.println("这是内部类方法");
            }
        }
        Inner inner = new Inner();
        inner.in();
    }
}
```

```java
Outer outer = new Outer();
outer.method();
```

### 匿名类

通过new 接口， 直接在内部重写方法， 实例化对象

```java
interface UserService{
    void say();
}
```

```java
public class Main {

    public static void main(String[] args) {
        UserService userService = new UserService() {

            @Override
            public void say() {
                System.out.println("说话");
            }
        };
        userService.say();
    }
}
```

## 异常

### 自定义异常

```java
public class MyException extends Exception{
    // 重写异常信息
    @Ovrerride
    public String toString(){
        return "xxx"
    }
}
```

```java
throw new MyException();
```

## 多线程

### 三种实现方式

#### Thread

#### 创建

继承Thread类， 启动线程时， 不一定会立刻执行， 执行顺序由cpu控制

```java
public class TestThread extends Thread{

    public static void main(String[] args) {
        TestThread testThread = new TestThread();
        testThread.start();

        for (int i = 0; i < 2000; i++) {
            System.out.println("主线程" + i);
        }

    }
    
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("次线程" + i);
        }

    }
}
```

#### 实例

```java
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;
import java.net.URL;

/**
 * @author itianeru
 * @date 2020/7/19
 */
public class Test extends Thread{

    private String url;
    private String name;

    public Test(String url, String name){
        this.url = url;
        this.name = name;
    }


    public static void main(String[] args) {
        Test test1 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852506&di=1fe99d55e1b4b18fbf47adc0c898d49f&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170420%2F20170420105628_ea6da92abc46098d8e03ad2ee55abeb7_9.jpeg", "美女01.jpg");
        Test test2 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852505&di=30ef34f705714e077873220ad85c2907&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170303%2F20170303094555_7851ce3d965701f3201d4df2dde56592_8.jpeg", "美女02.jpg");
        Test test3 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153964235&di=8119453820aff3a6b5994ce6daff5572&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170504%2F20170504151712_e0c8bd4e0919dba5f9772757c2b34304_1.jpeg", "美女03.jpg");

        test1.start();
        test2.start();
        test3.start();
    }

    @Override
    public void run() {
        WebDownloader webDownloader = new WebDownloader();
        webDownloader.downloader(url, name);
        System.out.println("下载了文件："+ name);
    }
}

class WebDownloader{
    /**
     * 文件下载器
     * @param url 下载地址
     * @param name 文件名
     * @return void
     */
    public void downloader(String url, String name){
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("io异常");
        }
    }
}
```

#### Runnable

#### 创建

实现Runnable接口

```java
public class TestThread implements Runnable{

    public static void main(String[] args) {
        TestThread testThread = new TestThread();
        Thread thread = new Thread(testThread);
        thread.start();

        for (int i = 0; i < 2000; i++) {
            System.out.println("主线程" + i);
        }

    }

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("次线程" + i);
        }

    }
}
```

#### 实例

```java
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;
import java.net.URL;

/**
 * @author itianeru
 * @date 2020/7/19
 */
public class Test implements Runnable{

    private String url;
    private String name;

    public Test(String url, String name){
        this.url = url;
        this.name = name;
    }


    public static void main(String[] args) {
        Test test1 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852506&di=1fe99d55e1b4b18fbf47adc0c898d49f&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170420%2F20170420105628_ea6da92abc46098d8e03ad2ee55abeb7_9.jpeg", "美女01.jpg");
        Test test2 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852505&di=30ef34f705714e077873220ad85c2907&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170303%2F20170303094555_7851ce3d965701f3201d4df2dde56592_8.jpeg", "美女02.jpg");
        Test test3 = new Test("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153964235&di=8119453820aff3a6b5994ce6daff5572&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170504%2F20170504151712_e0c8bd4e0919dba5f9772757c2b34304_1.jpeg", "美女03.jpg");

        new Thread(test1).start();
        new Thread(test2).start();
        new Thread(test3).start();
    }

    @Override
    public void run() {
        WebDownloader webDownloader = new WebDownloader();
        webDownloader.downloader(url, name);
        System.out.println("下载了文件"+ name);
    }
}

class WebDownloader{
    /**
     * 文件下载器
     * @param url 下载地址
     * @param name 文件名
     * @return void
     */
    public void downloader(String url, String name){
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("io异常");
        }
    }
}
```

#### Callable

- **实现Callable接口， 需要返回值类型**
- **重写call方法， 需要抛出异常**
- **创建目标对象**
- **创建执行服务： **ExexcutorService service = Excutors.newFixedThreadPool(1);
- **提交执行:** Future\<Boolean> result = service.submit(t);
- **获取结果:** boolean r = result.get();
- **关闭服务:** service.shutdownNow(); 

#### 实例

```java
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.util.concurrent.*;

public class TestCallable implements Callable<Boolean> {
    private String url;
    private String name;

    public TestCallable(String url, String name){
        this.url = url;
        this.name = name;
    }


    public static void main(String[] args) throws ExecutionException, InterruptedException {
        TestCallable test1 = new TestCallable("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852506&di=1fe99d55e1b4b18fbf47adc0c898d49f&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170420%2F20170420105628_ea6da92abc46098d8e03ad2ee55abeb7_9.jpeg", "美女01.jpg");
        TestCallable test2 = new TestCallable("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153852505&di=30ef34f705714e077873220ad85c2907&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170303%2F20170303094555_7851ce3d965701f3201d4df2dde56592_8.jpeg", "美女02.jpg");
        TestCallable test3 = new TestCallable("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595153964235&di=8119453820aff3a6b5994ce6daff5572&imgtype=0&src=http%3A%2F%2F00.minipic.eastday.com%2F20170504%2F20170504151712_e0c8bd4e0919dba5f9772757c2b34304_1.jpeg", "美女03.jpg");

        // 线程池
        ExecutorService service = Executors.newFixedThreadPool(3);

        // 提交执行
        Future<Boolean> r1 = service.submit(test1);
        Future<Boolean> r2 = service.submit(test2);
        Future<Boolean> r3 = service.submit(test3);

        // 获取结果
        boolean b1 = r1.get();
        boolean b2 = r2.get();
        boolean b3 = r3.get();

        System.out.println(b1);
        System.out.println(b2);
        System.out.println(b3);
        service.shutdownNow();
    }

    @Override
    public Boolean call(){
        WebDownloader webDownloader = new WebDownloader();
        webDownloader.downloader(url, name);
        System.out.println("下载了文件"+ name);
        return true;
    }
}

class WebDownloader{
    /**
     * 文件下载器
     * @param url 下载地址
     * @param name 文件名
     * @return void
     */
    public void downloader(String url, String name){
        try {
            FileUtils.copyURLToFile(new URL(url), new File(name));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("io异常");
        }
    }
}
```

### 线程状态

Thread.State

- **创建(新生)** 

  ```java
  // 创建线程对象, 进入新生状态
  Thread thread = new Thread();
  ```

- **就绪**  

  ```java
  // 进入就绪状态
  thread.start();
  ```

- **运行**

  cpu调度进入运行状态

- **阻塞**

  阻塞解除后, 会从新进入就绪状态

  ```java
  // 进入阻塞状态
  thread.sleep();
  // 或
  thread.wait();
  ```

- **死亡**

  线程中断或者结束, 进入死亡状态, 不能再次启动

### 线程方法

- **setPriority(int newPriority)**

  更改线程优先级, 优先级高的线程, 容易被cpu执行

  最大值为10;

  ```
  Thread thread = new Thread(testPriority);
  thread.setPriority(10);
  thread.start();
  ```

  

- **static void sleep(long millis)**

  在指定毫秒数内让当前线程体休眠

  ```java
  Thread.sleep()
  ```

- **void join()**

  等待该线程终止再执行其他线程

  ```java
  public void statci main(String[] arg){
      TestJoin testJoin = new TestJoin();
      Thread thread = new Thread(testJoin)
      thread.start();
      for(int i=0; i<100; i++){
          if(i == 50){
              // 插队
              thread.join();
          }
          System.out.print("main:" + i);
      }
  }
  
  ```

- **static void yield()**

  暂停当前正在执行的线程对象, 并执行其他线程

  ```java
  // 让线程从运行状态, 变回就绪状态, cpu会重新进行调度, 有可能cpu又选择当前线程进行运行, 导致yield失败, 
  public void run(){
      System.out.print("start");
      Thread.yield();
      System.out.print("end");
  }
  
  ```

- **void interrupt()**

  中断线程(不推荐)

- **boolean isAlive()**

  测试线程是否处于活动状态

### 线程停止

- 利用循环次数
- 利用标志位
- 不要使用stop()或者 interrupt(), 等过时以及JDK不推荐使用的方法

```java
public class TestStop implements Runnable {

    private boolean flag = true;
    @Override
    public void run() {
        int i = 0;
        while (flag){
            System.out.println("run" + i++);
        }
    }

    private void stop(){
        this.flag = false;
    }

    public static void main(String[] args) {
        TestStop testStop = new TestStop();
        new Thread(testStop).start();
        
        for (int i = 0; i < 1000; i++) {
            System.out.println("mian" + i);
            if (i == 900){
                testStop.stop();
                System.out.println("线程停止了");
            }
        }

    }
}
```

### 守护线程

- 线程分为守护线程和用户线程
- 虚拟机必须保证用户线程执行完毕
- 虚拟机不用等待守护线程执行完毕                                                                                                       
- 如, 后台记录操作日志, 监控内存, 垃圾回收等

```java
// 守护线程在其他线程结束后, 自动结束
Thread thread = new Thread(testDaemon);
thread.setDaemon(true);                                                                        
thread.start();
```

### 线程同步

#### synchronized

##### 同步方法

synchronized方法控制对象访问, 每个对象对应一把锁, 每个synchronized方法都必须获得该方法的对象的锁才能执行, 否则线程会阻塞, 方法一旦执行, 就会独占该锁, 直到方法结束才会释放锁, 后面被阻塞的线程才能获得这个锁,继续执行.

```java
public synchronized void method(int args){}
```

###### 例子

```java
package com.itianeru.demo;

/**
 * @author itianeru
 * @date 2020/7/19
 */
public class BuyTicket implements Runnable{

    private int ticketNums = 10;

    private boolean flag = true;

    public static void main(String[] args) {
        BuyTicket ticket = new BuyTicket();
        new Thread(ticket, "路人A").start();
        new Thread(ticket, "路人B").start();
        new Thread(ticket, "路人C").start();
    }


    @Override
    public void run() {
        while (flag){
            try {
                buy();
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }

    public synchronized void buy(){
        if (ticketNums <= 0){
            flag = false;
            return;
        }

        System.out.println(Thread.currentThread().getName() + "拿到了"+ ticketNums-- + "票");
    }
}
```

##### 同步块

Obj称为同步监视器

- Obj可以是任何对象, 但是推荐使用共享资源作为同步监视器
- 同步方法中无需指定同步监视器, 因为同步方法中, 同步监视器就是this, 就是这个对象本身, 或者是class(反射对象).

执行过程

- 第一个线程访问, 锁定同步监视器
- 第二个线程访问, 发现同步监视器被锁定, 无法访问
- 一个线程访问结束, 释放同步监视器
- 第二个线程访问, 发现同步监视器没有锁, 然后锁定并访问

```java
synchronized(Obj){}
```

###### 例子

```java
public class Bank{
    public static void main(String[] args) {
        Account account = new Account(100, "结婚基金");
        Drawing you = new Drawing(account, 50,"你");
        Drawing me = new Drawing(account, 100,"我");
        you.start();
        me.start();
    }
}

class Account{
    int money;
    String name;

    public Account(int money, String name) {
        this.money = money;
        this.name = name;
    }
}

class Drawing extends Thread{
    Account account;
    int drawingMoney;
    int nowMoney;

    public Drawing(Account account, int drawingMoney, String name) {
        super(name);
        this.drawingMoney = drawingMoney;
        this.account = account;
    }

    @Override
    public void run() {
        synchronized (account){
            if(account.money-drawingMoney<0){
                System.out.println(Thread.currentThread().getName() + "钱不够, 取不了");
                return;
            }

            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            account.money = account.money - drawingMoney;

            nowMoney = nowMoney + drawingMoney;

            System.out.println(account.name + "余额为" + account.money);
            System.out.println(this.getName() + "手里的钱" + nowMoney);
        }

    }
}
```

#### lock

- Lock是显式锁， synchronized是隐式锁
- Lock只有代码块锁
- 使用Lock锁， JVM将花费较少的时间来调度线程， 性能更好， 并且有更好的扩展性
- 优先使用顺序： Lock -> 同步代码块 -> 同步方法

```java
public class TestLock {
    public static void main(String[] args) {
        TestLock2 testLock2 = new TestLock2();
        new Thread(testLock2).start();
        new Thread(testLock2).start();
        new Thread(testLock2).start();
    }
}

class TestLock2 implements Runnable {
    private int ticketNums = 10;
    // 可重用锁
    private final ReentrantLock lock = new ReentrantLock();
    @Override
    public void run() {
        while (true){
            lock.lock();
            try {
                if (ticketNums>0){
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + ":"+ticketNums--);
                }else{
                    break;
                }
            } finally {
                lock.unlock();
            }
        }
    }
}
```



### 死锁

避免死锁的四个必要条件

- 互斥条件： 一个资源每次只能被一个进程使用
- 请求与保持条件： 一个进程因请求资源而阻塞时， 对已获得的资源保持不放
- 不剥夺条件： 进程已获得的资源， 在未使用完之前， 不能强行剥夺
- 循环等待条件： 若干进程之间形成一种头尾相接的循环等待资源关系

只要避免其中一个， 就可避免死锁

### 线程协作

- wait():  表示线程一直等待， 直到其他线程通知， 与sleep不同， 会释放锁
- notify(): 唤醒一个处于等待状态的线程
- notifyAll(): 唤醒同一个对象上所有调用wait方法等待的线程， 优先级别高的线程优先调度

#### 管程法

```java
// 测试生产者消费模型， 利用缓冲区解决： 管程法
public class testPC {
    public static void main(String[] args) {
        SynContainer container = new SynContainer();
        new Producter(container).start();
        new Consumer(container).start();
    }
}


// 生产者
class Producter extends Thread{
    SynContainer container;
    public Producter(SynContainer container){
        this.container = container;
    }
    @Override
    public void run(){
        for (int i=1; i<=100; i++){
            System.out.println("生产了" + i + "只鸡");
            try {
                container.push(new Chicken(i));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

}

// 消费者
class Consumer extends Thread{
    SynContainer container;
    public Consumer(SynContainer container){
        this.container = container;
    }
    @Override
    public void run() {
        for (int i = 1; i <= 100; i++) {
            try {
                Chicken chicken = container.pop();
                System.out.println("消费的鸡编号为-->"+chicken.getId());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Chicken{
    private int id;
    public Chicken(int id) {
        this.id = id;
    }
    public int getId() {
        return id;
    }
}

class SynContainer{
    Chicken[] chickens = new Chicken[10];
    int count = 0;
    public synchronized void push(Chicken chicken) throws InterruptedException {
        if (count == chickens.length){
            this.wait();
        }
        chickens[count] = chicken;
        count++;
        this.notifyAll();
    }
    public synchronized Chicken pop() throws InterruptedException {
        if (count == 0){
            this.wait();
        }
        count--;
        Chicken chicken = chickens[count];
        this.notifyAll();
        return chicken;
    }
}
```

#### 信号灯法

```java
public class testPC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Player(tv).start();
        new Watcher(tv).start();
    }
}
class Player extends Thread{
    TV tv;
    public Player(TV tv){
        this.tv = tv;
    }
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            if (i%2 == 0){
                this.tv.play("快乐大本营播放中");
            }else{
                this.tv.play("广告···");
            }
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class Watcher extends Thread{
    TV tv;
    public Watcher(TV tv){
        this.tv = tv;
    }
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            this.tv.watch();
        }
    }
}

class TV{
    String voice;
    boolean flag = true;
    public synchronized void play(String voice){
        System.out.println("演员表演了" + voice);
        this.notifyAll();
        this.voice = voice;
        this.flag = !this.flag;
    }
    public synchronized void watch(){
        if (flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观看了"+ voice);
        this.notifyAll();
        this.flag = !this.flag;
    }
}
```

### 线程池

```java
public class TestPool {
    public static void main(String[] args) {
        // 不推荐使用
        ExecutorService service = Executors.newFixedThreadPool(10);
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.shutdownNow();
    }
}

class MyThread implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```



## 代理

### 静态代理

代理真实对象， 在完成真实对象的功能, 同时， 并添加新的功能

```java
public class StaticProxy{
    public static void main(String[] args) {
        MarryCompany marryCompany = new MarryCompany(new You());
        marryCompany.marry();
    }
}

interface Marry{
    void marry();
}

// 真实角色
class You implements Marry{
    @Override
    public void marry() {
        System.out.println("结婚了");
    }
}

// 代理角色
class MarryCompany implements Marry{
    private Marry target;
    public MarryCompany(Marry target){
        this.target = target;
    }
    @Override
    public void marry() {
        before();
        this.target.marry();
        after();
    }
    private void after() {
        System.out.println("结束了");
    }
    private void before() {
        System.out.println("开始了");
    }
}
```

## Lamda表达式

### 推导过程

先定义一个接口

```java
interface IUser{
    void say();
}
```

外部类实现接口

```java
public class UserImpl implements IUser{
    @Override
    public void say(){
        System.out.print("Hello World");
    }
}
```

静态内部类实现接口

```java
public class Main{
    static class UserImpl implements IUser{
        @Override
        public void say(){
            System.out.print("Hello World");
        }
    }
}
```

局部内部类实现接口

```java
public class Main{
    public static void main(String[] args) {
        class UserImpl implements IUser{
            @Override
            public void say(){
                System.out.print("Hello World");
            }
        }
    } 
}
```

匿名类

```java
public class Main{
    public static void main(String[] args) {
        IUser user = new IUser(){
            @Override
            public void say(){
                System.out.print("Hello World");
            }
        }
    } 
}
```

Lamda

```java
public class Main{
    public static void main(String[] args) {
        IUser user = (String a) -> {
            System.out.print("Hello World" + a);
        }
        // 简化
        user = a -> System.out.print("Hello World" + a);
        
    } 
}
```