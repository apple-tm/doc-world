# MySQL 历史和架构



#### 架构图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191002173904283.png)

1. 第一层： 对客户端链接处理、安全验证，每个客户端都会在服务端有一个线程
2. 第二层：查询解析、分析、查询缓存、内置函数、存储过程、触发器、视图
   1. select操作会先检查是否命中查询`缓存`，命中则直接返回缓存数据，否则解析查询并创建对应的解析树。
   2. 解析器会将命令分类为`select、dml、ddl、rep、status五个模块`的内容将给不同的模块去处理。
   3. 实例： select uid,name from user where gender = 1;
      1. 这个select 查询先根据where 语句进行选取，而不是先将表全部查询出来以后再进行过滤
      2. 这个select查询先根据uid和name进行属性投影，而不是将属性全部取出以后再进行过滤
      3. 将这两个查询条件联接起来生成最终查询结果
      4. **查询缓存往往弊大于利**：因为mysql中的数据经常变化，所以使用缓存的命中率很低，<b>**mysql8删除了该功能**<b>。



3. 第三层：存储引擎，负责数据的存储和提取，MySQL服务器通过API与存储引擎通信，屏蔽了各种引擎之间的差异，常见的存储引擎有：InnoDB、MyISAM。MySQL 5.5之后，**默认的存储引擎由MyISAM变为InnoDB**。

| -        | Innodb                                   | Myisam                                                 |
| -------- | ---------------------------------------- | ------------------------------------------------------ |
| 存储文件 | （1） .frm 表定义文件（2） .ibd 数据文件 | （1）.frm 表定义文（2）.myd 数据文件（3）.myi 索引文件 |
| 锁       | 表锁、行锁                               | 表锁                                                   |
| 事务     | ACID                                     | 不支持                                                 |
| CRDU     | 读、写                                   | 读多                                                   |
| count    | 扫表                                     | 专门存储的地方                                         |
| 索引结构 | B+ Tree                                  | B+ Tree                                                |



淘宝网数据库架构从垂直拆分到水平拆分

水平拆分：

1. ID取模，insert 和 query 时对表名 动态拼接ID 取模值命中表，所有数据表结构没变，非数字型字段拆分可以先 MD5 转为 16 进制在取模...

垂直拆分：

1. 某个字段值很长，查询没必要查出来，单独拆分为一张表，使用 ID 关联



#### 大数据量

10TB 以上的存储建议使用 infobright**数据仓库**

1. 同等的SELECT查询语句，速度比MyISAM、InnoDB等普通的MySQL存储引擎快5～60倍，测试时，仅用了原来时间的四分之一；
2. 高压缩比：在我使用的项目中为17:1，极大地节省了数据存储空间；
3. 基于列存储：无需建索引，无需分区；
4. 适合复杂的分析性SQL查询：SUM, COUNT, AVG, GROUP BY；
5. 不支持数据更新：社区版Infobright只能使用“LOAD DATA INFILE”的方式导入数据，不支持INSERT、UPDATE、DELETE；
6. 不支持高并发：只能支持10-18多个并发查询；



#### CPU 密集型

以复杂计算为主的程序，计算圆周率、对视频进行高清解码（直播视频耗性能）

线程数 = CPU 核心数时为最大性能

加多线程会造成性能下降，因为频繁切换线程消耗资源

消耗CPU资源，因此，代码运行效率至关重要。Python这样的脚本语言运行效率很低，完全不适合计算密集型任务。对于计算密集型任务，**最好用C语言**编写。



#### IO 密集型

数据库交互、文件下载、网络传输，比如Web应用

线程数 = 2*CPU 核心数，提高 CPU 利用率

用运行速度极快的C语言替换用Python这样运行速度极低的脚本语言，完全无法提升运行效率。对于IO密集型任务，最合适的语言就是开发效率最高（代码量最少）的语言，**脚本语言是首选**，C语言最差。