---
title: Redis故障迁移对客户端的影响
date: 2018-07-19 21:47:48
tags: 
    - redis
    - sentinel
    - cluster
    - fault
categories: redis
---

# 测试用例
10个线程死循环执行get set操作，每轮间隔10ms，日志打印执行结果

# sentinel
## 场景1，master挂掉，sentinel选举生成新的master
客户端连接master进行redis操作，master挂掉后，客户端依然向原master发起请求，这是会抛出连接异常
`redis.clients.jedis.exceptions.JedisConnectionException: java.net.ConnectException: Connection refused:` connect，待sentinel生成新主后客户端重新初始化连接池，服务可用，中间服务异常9s左右
模拟测试，直接kill master进程

## 场景2，master没挂，sentinel选举新的master
原master变为slave，节点变为只读，客户端访问会抛出节点只读异常
`redis.clients.jedis.exceptions.JedisDataException: READONLY You can't write against a read only slave.`
服务不可用30s左右
模拟测试，使用slaveof ip port使master变为slave，sentinel启动选举

# cluster
## 场景1，master挂掉，集群把slave升为master

客户端调用会阻塞，直到服务可用，不会抛出异常，阻塞时间22s
模拟测试，使用debug segfault命令模拟节点宕机

## 场景2，有节点网络问题或硬件问题导致心跳超时

集群节点心跳超时，集群不健康导致服务不可用，客户端调用集群会抛出cluster is down的异常，心跳超时设置为5s
使用tc工具制造网络延迟
``` 
$ tc qdisc add dev eth0 root netem delay 7000ms  制造7s延迟的网络环境
$ tc qdisc del dev eth0 root  关闭延迟
```
延迟6s  slave升为master，原master没有降为slave，状态fail，connected，客户端请求异常，持续4分钟服务不可用（可能是bug），关闭tc，原master降为slave，服务恢复
延迟7s   slave升为master，原master没有降为slave，状态fail，connected，客户端阻塞14s，之后服务可用
延迟10s slave升为master，客户端阻塞20s，期间间隔10s处还有一次调用成功

## 小插曲
kill master（A）后，重启节点A，集群中A的状态为master，fail，disconnected，重启服务后大概需要30s左右才会降为slave，并加载数据（key 3699162个）
重启后立即开始测试，7s延迟也会出现服务一直不可用情况

# 小结
1. 大部分场景下cluster主从架构的服务可用性比sentinel更高，客户端调用阻塞而不是异常；
2. 少数场景，比如cluster的节点网络延迟略大于心跳超时时间，集群会处于非健康状态，不能提供服务

## 集群故障时sentinel抛异常而cluster只是阻塞原因推测
  sentinel是一个监控集群，实际客户端连接的是redis的master节点，当master宕机后或被降级为slave后，客户端依然会向master发起请求，这是服务不可用，会抛出连接异常或只读异常，直至sentinel通知客户端master换人了，客户端重新加载连接池，连接新的master继续提供服务
  cluster模式下，客户端连接的整个cluster集群，请求发送到cluster后会根据key做路由再做处理，路由的节点在发生主从切换时不是直接返回异常而是阻塞，等待节点正常，主从切换异常情况除外，异常情况集群处于非健康状态，直接返回客户端cluster is down异常，服务不可用
