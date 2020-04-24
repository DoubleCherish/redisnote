### Redis缓存一致性方案记录1

**方案**：异步更新缓存（基于订阅binlog的同步机制）

​		1、**读**：热点数据放入Redis供应用读，读不到再去mysql读

​		2、**写**：增(insert)删(delete)改(update)都用直接操作mysql

​		3、**缓存更新**：mysql开启log_bin，将成功的写操作都记录到binlog，通过模仿mysql的slave方式来订阅binlog，有新数据就推送给mq，执行异步更新redis缓存或者直接删除对应改变的key，让其重新从数据库中读取。

**示例**：

​		组件： Mysql，[阿里Canal](<https://github.com/alibaba/canal>)，redis

**注**：阿里Canal是一款订阅消费Mysql binlog日志的中间件，分为Client和Server。Server有点类似Mysql备库，从主Mysql获取其增量的binlog数据，Client消费Server的新数据。

...后面补充