### ShardingSphere

#### 参考文档
- https://shardingsphere.apache.org/document/5.2.0/cn/overview/

#### 产品
- ShardingSphere-JDBC: 客户端维度
- ShardingSphere-Proxy: 服务端维度

#### 分库分表带来的问题
- 分布式事务: XA 协议
- 主键重复: 基于雪花算法、UUID
- 数据迁移: 全量 + 增量 + 平滑过度
- 连表查询: 绑定表
- 归并查询: 归并引擎

#### 分布式事务
- 基于XA协议: 两阶段提交(2PC)，需要数据库层面的支持
  - 第一阶段: prepare, TM(事务管理器) 广播指令, RM(资源管理器) 执行 SQL 并锁定资源, 但不提交
  - 第二阶段: commit, 所有参与的 RM 都返回成功(Prepared), TM 会广播 Commit 指令, 要求所有 RM 提交事务
  - 第二阶段: rollback, 有任何一个 RM 返回失败(例如资源不可用、操作失败、网络问题等), TM 会发出 Rollback 指令，要求所有参与的 RM 回滚事务

- BASE 事务: Seata 柔性事务，需要 seata server
 - 第一阶段: commit, 直接提交, 引擎拦截 SQL 自动生成反向 undo Log, 随业务数据在同一个本地事务中提交, 本地锁释放瞬间阻塞消失
 - 第二阶段: delete, 主线程返回, 后台进程负责收尾, 如果是 commit 直接异步删掉 undo Log, 如果是 rollback 利用 undo Log 反向补偿


#### 主键重复
- 基于雪花算法: 64位, 1位符号位 + 41位时间戳 + 10位机器ID + 12位序列号
- 基于UUID

#### 连表查询
- 避免连表查询，在业务层处理掉
- 如果实在避免不了，直接使用 es 创建索引表

#### 基于 Redis 主键生成方案
- redis 维护一个key, 值是整数类型。
- 集群中的pod，向 redis 申请一个号段，并维护在 JVM 中使用。start ~ end
- 等到使用完成 start > end 的时候，再重新申请一段。
  ```伪代码
  map key -> cache;
  generateByCache(key):
    lock(本地锁);
      cache = map.get(key);
      if cache == null || start > end:
        last = redis.get(key);
        if last == null:
          lock(分布式锁);
            last = redis.get(key);
            if last == null:
              set key value from DB;
          unlock(分布式锁);
        end = redis.incrby(key, 1000);
        map.put(key, cache);
      cache = map.get(key);
      return cache.start++;
    unlock(本地锁);
  ```