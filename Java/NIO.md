# NIO



#### 概述

1. JDK1.4 start
2. Non-block I/O
3. **高并发网路支持**



#### sleep wait

1. sleep是Thread类的方法,wait是Object类中定义的方法
2. 两个方法**都会影响线程的执行行为**，但是本质上是有区别的
3. Thread.**sleep不会导致锁行为**的改变，如果当前线程是拥有锁的，那么Thread.sleep不会让线程释放锁。
4. **锁相关的方法都定义在Object类中**，因此调用Thread.sleep是不会影响锁的相关行为。
5. **都会暂停当前的线程**，对于CPU资源来说，不管是哪种方式暂停的线程，都表示它暂时不再需要CPU的执行时间
6. **OS会将执行时间分配给其它线程**。区别是，**调用wait后，需要别的线程执行notify/notifyAll才能够重新获得CPU**执行时间。
7. sleep不释放锁 线程是进入阻塞状态还是就绪状态？进入**阻塞状态**



#### BIO缺点

1. 阻塞 IO模型
2. 弹性伸缩差，一个连接一个线程，服务器压力大
3. 多线程消耗资源



#### Channel

1. **双向性，可读可写**，通道**可以异步地读写**，BIO 流单向，输入流读，输出流写
2. 非阻塞性
3. 操作唯一,**只能从 Buffer 中读写**
4. 实现类
   1. 文件类：FileChannel
   2. UDP 类：DatagramChannel
   3. TCP 类：ServerSocketChannel、SocketChannel
5. Channel 有点**象流**。 数据可以从Channel读到Buffer中，也可以从Buffer 写到Channel中。

#### Buffer

1. 定义：（可以模拟为字节数组）读写 Channel,切换读写模式flip()，position 和 limit 会改变限制读写区域
2. capacity
3. position
4. limit
5. mark：reset()position重置为标记的位置



#### Selector

1. 多路复用器，允许**单线程处理多个 Channel**
2. 应用场景：应用打开了**多个连接（通道），但每个连接的流量都很低**，使用Selector就会很方便
3. 工作流程：
   1. 向Selector**注册Channel**
   2. **调用它的select()**方法。这个**方法会一直阻塞到某个注册的通道有事件就绪**。
   3. **一旦这个方法返回，线程就可以处理这些事件**，事件的例子有如新连接进来，数据接收等
4. SelectionKey
   1. 连接就绪
   2. 接收就绪
   3. 读就绪
   4. 写就绪

