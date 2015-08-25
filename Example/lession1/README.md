# mongodb 副本集

主从复制是MongoDB最常用也是最简单的复制操作。常用于数据备份和故障修复等。

[学习博客](http://www.cnblogs.com/wly923/archive/2013/04/14/3020663.html)

## 副本集介绍

副本集有`3`种节点

| 英文别名 | 中文名称 | 阐述 |
|---------|--------|------|
| standard | 常规节点 | 既存储完整的数据副本又参加投票，可以成为活跃节点 |
| passive  | 副本节点 | 存储完整的数据副本又参加投票，但是不可以成为活跃节点 |
| arbiter  | 仲裁节点 | 只参与投票，不接受数据的复制，不可以成为活跃节点 |

## 如何配置

### 通过配置文件（*.conf）启动数据库

#### 通用配置

```
dbpath=${DB_PATH}
bind_ip=@{HOST_IP}
port=@{PORT}
replSet=replicademo/127.0.0.1:11111
```

**replSet**："副本集名称”/主机地址"（端口号必须存在）

### 如何开始测试

开始前，需要对配置文件上的`dbpath`进行配置.

####  启动mongodb

```
// 启动数据库
mongod -f replica1.conf
mongod -f replica2.conf
mongod -f replica3.conf
```

#### 配置副本集

```
// 连上主机
mongo 127.0.0.1:11111

// 使用库 `admin`
use admin

// 配置副本集
db.runCommand({"replSetInitiate":
    {
        "_id":'replicademo',
        "members":[]
               {
                "_id":1,
                "host":"127.0.0.1:11111"
               },
               {
                "_id":2,
                "host":"127.0.0.1:22222"
               }
        ]
    }
})

// 查看配置项
rs.status()

// 添加副本集
rs.add("127.0.0.1:33333")

// 移除副本集
rs.remove("127.0.0.1:33333")

```
