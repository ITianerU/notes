# 概念

## 简介

- Netty是一个异步. 基于事件驱动的网络应用框架, 用于快速开发高性能, 高可靠性的网络IO程序
- Netty主要针对在TCP协议下, 面向Clients端的高并发应用, 或者Peer-to-Peer场景下的大量数据持续传输的应用
- Netty本质是一个NIO框架, 适用于服务器通讯相关的多种应用场景

## 架构

分为四层

- 底层 TCP/IP
- 倒数第二层  JDK(io/网络)
- 第二层 NIO(优化了JDK)
- 最上层 Netty(基于NIO)

## IO模型

**概述:** 用什么样的通道进行数据的发送和接受, 很大程度上决定了程序通信的性能

目前有三种网络编程模型: BIO, NIO, AIO

### BIO(blocking I/O)

Java的原生IO, **同步并阻塞**, 服务器实现模式为一个连接一个线程, 即客户端有连接请求时服务器端就需要启动一个线程进行处理, 如果这个连接不做任何事情会造成不必要的线程开销, 如果客户端数量很多服务端需要启用大量的线程与之连接, 影响服务端性能, 可通过线程池改善

**适用场景**: BIO方式适用于连接数较小且固定的架构, 这种方式对服务器资源要求较高, 并发局限于应用中

#### 应用实例

```java
public class BIOServer {
    public static void main(String[] args) throws Exception {
        // 创建线程池
        ExecutorService executorService = Executors.newCachedThreadPool();
        // 如果有客户端要连接, 就创建一个线程, 与之通讯, 端口号是6666
        ServerSocket serverSocket = new ServerSocket(6666);
        System.out.println("服务器启动了");
        while (true) {
            try {
                // 监听, 等待客户端连接
                Socket socket = serverSocket.accept();
                System.out.println("连接到一个客户");
                // 创建一个线程, 与之通讯
                executorService.execute(() -> handler(socket));
            } catch (IOException e) {
                break;
            }
        }
    }
    // 编写一个handler方法, 和客户端通讯
    public static void handler(Socket socket){
        try {
            System.out.printf("线程id => %s, 线程name => %s%n", Thread.currentThread().getId(), Thread.currentThread().getName());
            byte[] bytes = new byte[1024];
            // 通过socket获取输入流
            InputStream inputStream = socket.getInputStream();
            // 循环读取客户端发送的数据
            while (true){
                int read = inputStream.read(bytes);
                if(read != -1){
                    System.out.printf("线程id => %s, 线程name => %s%n", Thread.currentThread().getId(), Thread.currentThread().getName());
                    // 输出客户端发送的数据
                    System.out.println(new String(bytes, 0, read));
                }else{
                    break;
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            System.out.println("关闭和client的连接");
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 测试

```shell
# cmd
telnet 127.0.0.1 6666
# 按 ctrl + ]
send msg
```

### NIO(non-blocking I/O)

**同步非阻塞**, 服务器实现模式为一个线程处理多个请求(连接), 即客户端发送的连接请求都会注册到多路复用器(**Selector**)上, 服务端仅用一个线程去管理多路复用器, 多路复用器通过轮循, 确定客户端连接有I/O请求就进行处理

**适用场景:** NIO方式适用于连接数目多且连接比较短的架构, 比如聊天服务器, 弹幕系统, 服务器之间的通讯, 编程较为复杂

#### 三大核心组件  

- **Selector(选择器)**  
  - 一个线程, 对应着一个选择器
  - 选择器会根据不同的事件在通道中进行切换
- **Channel(通道)** 
  - 一个选择器对应多个通道, 通道在创建时会注册到选择器中,
  - 选择器去选择通道, 在通道中进行读写
  - 可以理解为, 一个通道就是一个连接
  - 通道也是双向的
- **Buffer(缓冲区)**  
  - 本质是一个内存块, 底层是一个数组
  -  一个通道对应一个缓冲区, 通道和缓冲区是双向的, 
  - 客户端程序是从缓冲区读写数据, 对通道和客户端之间有一个缓冲作用
  - Buffer既可以读又可以写, 需要flip方法切换, 与之对比BIO中输入输出流都是单向的

#### 应用实例

##### Buffer

**基本用法**

```java
public class BasicBuffer {
    public static void main(String[] args) {
        // 创建buffer, 大小为5
        IntBuffer intBuffer = IntBuffer.allocate(5);
        // 向buffer中存放数据
        for (int i = 0; i < intBuffer.capacity(); i++) {
            intBuffer.put(i * 2);
        }
        // 从buffer读取数据
        // 将buffer转换, 读写切换, 读->写 写->读都需要调用一次
        intBuffer.flip();
        while (intBuffer.hasRemaining()) {
            // get()方法中维护了一个索引, 每get一次, 索引向后移动了一次
            System.out.println(intBuffer.get());
        }
    }
}
```

类型区分

```java
public class BasicBuffer {
    public static void main(String[] args) {
        // buffer可以存入任意类型的数据, 但是在取出时数据类型要对应
        ByteBuffer byteBuffer = ByteBuffer.allocate(128);
        byteBuffer.putInt(10);
        byteBuffer.putChar('A');

        byteBuffer.flip();
        System.out.println(byteBuffer.getInt());
        System.out.println(byteBuffer.getChar());
    }
} 
```

只读buffer

```java
public class ReadOnlyBuffer {
    public static void main(String[] args) {
        // 存入数据
        ByteBuffer byteBuffer = ByteBuffer.allocate(64);
        for (int i = 0; i < 64; i++) {
            byteBuffer.put((byte) i);
        }
        byteBuffer.flip();

        // 得到一个只读buffer
        ByteBuffer readOnlyBuffer = byteBuffer.asReadOnlyBuffer();
        while (readOnlyBuffer.hasRemaining()){
            System.out.println(readOnlyBuffer.get());
        }
        byteBuffer.flip();
        // 此时再写入, 会报错
        readOnlyBuffer.put((byte)1);
    }
}
```

映射buffer

```java
// 可以让文件直接在内存(堆外内存)中修改, 操作系统不需要拷贝一次
public class MappedByteBufferTest {
    public static void main(String[] args) throws IOException {
        // 读文件
        RandomAccessFile randomAccessFile = new RandomAccessFile("src/a.txt", "rw");
        // 获取通道
        FileChannel randomAccessFileChannel = randomAccessFile.getChannel();
        /*
        * 参数一: 模式
        * 参数二: 可以直接修改的起始位置
        * 参数三: 映射到内存的大小, 即, 可以将a.txt的多少个字节映射到内存
        * 将文件中指定位置的部分, 映射到内存中, 在内存中修改后再保存回文件中
        * */
        MappedByteBuffer mappedByteBuffer = randomAccessFileChannel.map(FileChannel.MapMode.READ_WRITE, 0, 5);
        // 修改
        mappedByteBuffer.put(0, (byte) 'H');
        // 索引不能大于指定的大小
        // mappedByteBuffer.put(5, (byte) '1');
        System.out.println((byte) 9);

        randomAccessFile.close();
        randomAccessFileChannel.close();
    }
}
```

分散,聚合

```java
/*
*  Scattering: 分散: 将数据写入到buffer时, 可以采用buffer数组, 以此写入(批量写入)
*  Gathering: 聚合: 将数据读出到buffer中, 可以采用buffer数组, 依次读写(批量写入)
* */
public class ScatteringAndGatheringTest {
    public static void main(String[] args) throws IOException {
        // 创建socket通道
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 创建ip对象, 并指定端口
        InetSocketAddress inetSocketAddress = new InetSocketAddress(8000);
        // 绑定端口
        serverSocketChannel.socket().bind(inetSocketAddress);
        // 设置buffer组, 最大容纳的字符数量
        ByteBuffer[] byteBuffers = new ByteBuffer[2];
        byteBuffers[0] = ByteBuffer.allocate(5);
        byteBuffers[1] = ByteBuffer.allocate(3);
        // 等待客户端连接
        SocketChannel socketChannel = serverSocketChannel.accept();
        // 最大接受字符数量
        int messageLength = 0;
        for(ByteBuffer byteBuffer : byteBuffers){
            messageLength += byteBuffer.capacity();
        }
        while (true) {
            // 接受客户端输入的字符数量
            int byteRead = 0;
            // 当前接收的数量小于最大接受数量, 就继续循环
            // 如果当前
            while (byteRead < messageLength){
                // 返回buffer接收到的个数, 如果未输入会阻塞
                // 输入的字符会自动填充到buffer数组中, 如果buffer数组被填满, 未被填充的字符会阻塞, 再运行到当前代码时, 会自动输入
                long l = socketChannel.read(byteBuffers);
                // 累计读取的字符数
                byteRead += l;
                System.out.println("byteRead=" + byteRead);
                for (ByteBuffer byteBuffer : byteBuffers) {
                    System.out.println("position=" + byteBuffer.position() + ",limit=" + byteBuffer.limit());
                }
            }
            // 反转
            Arrays.asList(byteBuffers).stream().forEach(byteBuffer -> byteBuffer.flip());
            
            long byteWrite = socketChannel.write(byteBuffers);
            System.out.println("byteWrite=" + byteWrite);
            Arrays.asList(byteBuffers).stream().forEach(byteBuffer -> byteBuffer.clear());
            System.out.println("byteRead=" + byteRead + ",byteWrite=" + byteWrite + ",messageLength=" + messageLength);
        }
    }
}
```

##### Channel

**基本用法**

写本地文件

```java
public class NIOFileChannel01 {
    // 将字符串写入到buffer中, 将buffer中数据写入到channel(通道)中, 通道写入到文件中
    public static void main(String[] args) throws IOException {
        String s = "hello, 老王";
        // 创建一个输出流
        FileOutputStream fileOutputStream = new FileOutputStream("D:\\code\\nowork\\java\\demo\\src\\a.txt");
        // 通过输出流获取文件channel
        FileChannel fileChannel = fileOutputStream.getChannel();
        // 创建一个缓冲区
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        // 将字符放入到buffer中
        byteBuffer.put(s.getBytes(StandardCharsets.UTF_8));
        // buffer写->读
        byteBuffer.flip();
        // 写入到channle中
        fileChannel.write(byteBuffer);
        // 关闭
        fileOutputStream.close();
        fileChannel.close();
    }
}
```

读本地文件

```java
public class NIOFileChannelRead {
    public static void main(String[] args) throws IOException {
        // 创建一个输入流
        File file = new File("D:\\code\\nowork\\java\\demo\\src\\a.txt");
        FileInputStream fileInputStream = new FileInputStream(file);
        // 输入流创建一个fileChannel
        FileChannel fileChannel = fileInputStream.getChannel();
        // 创建一个缓冲区
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        // 从输入流中读取字符并写入到缓冲区
        fileChannel.read(byteBuffer);
        // 输出
        System.out.println(new String(byteBuffer.array()));
        // 关闭
        fileInputStream.close();
        fileChannel.close();
    }
}
```

复制文件

```java
public class NIOFileChannelRead$Write {
    public static void main(String[] args) throws IOException {
        File fileA = new File("src/a.txt");
        FileInputStream fileInputStream = new FileInputStream(fileA);
        FileChannel fileInputStreamChannel = fileInputStream.getChannel();

        File fileB = new File("src/b.txt");
        FileOutputStream fileOutputStream = new FileOutputStream(fileB);
        FileChannel fileOutputStreamChannel = fileOutputStream.getChannel();

        ByteBuffer byteBuffer = ByteBuffer.allocate(128);
        while(true){
            // 如果文件过大, 字符数量超过缓冲区的大小, 需要多次读取多次写入
            int read = fileInputStreamChannel.read(byteBuffer);
            // read == -1时说明读取完毕
            if(read == -1){
                break;
            }
            byteBuffer.flip();
            fileOutputStreamChannel.write(byteBuffer);
            // 清空缓存
            byteBuffer.clear();
        }
        fileInputStream.close();
        fileInputStreamChannel.close();
        fileOutputStream.close();
        fileOutputStreamChannel.close();
    }
}
```

复制文件(自带api复制)

```java
public class NIOFileChannelCopy {
    public static void main(String[] args) throws IOException {
        // 读
        File fileA = new File("src/编译器背景图片.jpeg");
        FileInputStream fileInputStream = new FileInputStream(fileA);
        FileChannel fileInputStreamChannel = fileInputStream.getChannel();
        // 写
        String name = fileA.getName().split("\\.")[0] + "-副本";
        File fileB = new File("src/"+ name+ ".jpeg");
        FileOutputStream fileOutputStream = new FileOutputStream(fileB);
        FileChannel fileOutputStreamChannel = fileOutputStream.getChannel();
        // 从一个通道直接复制到另一个通道
        fileOutputStreamChannel.transferFrom(fileInputStreamChannel, 0, fileInputStreamChannel.size());
        // 关闭
        fileInputStream.close();
        fileInputStreamChannel.close();
        fileOutputStream.close();
        fileOutputStreamChannel.close();
    }
}
```

##### Selector



### AIO(不常用)

**异步非阻塞**, AIO引入异步通道的概念, 采用Proactor模式, 简化了程序的编写, 有效的请求才启动线程, 它的特点是先用操作系统完成后才通知服务端程序去启动线程去处理, 一般用于连接数较多且连接时间较长的应用

**适用场景**: AIO方式适用于连接数目多且连接比较长的架构, 比如相册服务器, 充分调用OS参与并发操作, 编程较为复杂

### 对比

|            BIO             |                    NIO                    |
| :------------------------: | :---------------------------------------: |
|     以流的方式处理数据     |            以块的方式处理数据             |
|         I/O效率慢          |                 I/O效率快                 |
|            阻塞            |                  非阻塞                   |
| 基于字节流和字符流进行操作 | 基于Channel(通道)和Buffer(缓冲区)进行操作 |

