# Args4j



#### 概述

1. 命令行参数解析工具
2. 对于 spring 项目没有意义，spring 项目直接使用配置文件
3. 应用场景：开发、测试、生产环境的各服务配置不同（比如超时时间、并行度、集群节点数、服务器器 URL）



#### 常用类

**Option**注解

- name指定该参数的名称
- aliases指定该参数的别名，
- usage解释该参数的作用
- metaVar解释该参数的类型
- required指定该参数是否为必须给定的

**CmdLineParser**解析命令行参数

parseArgument（args:_*）



