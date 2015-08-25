# mongodb 主从复制

主从复制是MongoDB最常用也是最简单的复制操作。常用于数据备份和故障修复等。

[学习博客](http://www.cnblogs.com/wly923/archive/2013/04/14/3019518.html)

## 如何配置

### 通过配置文件（*.conf）启动数据库

#### master

```
dbpath=${DB_PATH}
bind_ip=@{HOST_IP}
port=@{PORT}
master=true
```

#### slave

```
dbpath=${DB_PATH}
bind_ip=@{HOST_IP}
port=@{PORT}
slave=true
source=${MASTER_HOST_PORT}
```

### 如何开始测试

开始前，需要对配置文件上的`dbpath`进行配置.

####  启动mongodb

```
// 启动 Master
mongod -f master.conf

// 启动 Source
mongod -f source.conf
```

#### 主节点插入数据

```
// 连上主机
mongo 127.0.0.1:10001

// 使用库 `test`
use test

// 插入一条数据
db.user.insert({name:"test"});
```

#### 子节点查询数据

```
// 连接从机
mongo 127.0.0.1:10002

// 从机查询. 
db.user.find();
```

## 如何进行主从节点的转移

假设有2个节点，主节点A，子节点B，需要进行节点调换。

1. 暂停在A上使用fsync命令
2. 确定B是在同步节点A
3. 关闭B节点
4. 从B的DBPATH目录备份和移动的所有数据文件，并删除现有的数据local.sources。
5. 用master选项启动B
6. 往B中写入数据，用oplog设置新的同步开始时间点
7. 关闭B，当重启时B就有新的一组本地数据
8. 关闭A，用备份B的dbpath目录文件复制到A的dbpath中
9. 用master选项启动B
10. 用通常slave选项启动A，但要包括fastsync参数 

## 快照的概念

[快照基础概念](http://www.cnblogs.com/CareySon/archive/2012/03/30/2424880.html)

