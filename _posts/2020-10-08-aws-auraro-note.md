## aws aurora note

万能数据库的雏形

1.1 整体架构
计算引擎和物理存储分离 在不同的vpc里面
计算引擎为主从结构 1m 多从 主从之间通过 rds交互 提供监控集群状态 fail over等等 
主从在同一个region的不同az 保证az之间的latency在毫秒级

1.2&1.3 caching
计算层的cache 必须通过存储层的wal最终写入s3
通过顺序io提升性能
简化了计算层cache的设计 不需要刷盘
另外主从同步也不需要计算层参与 通过s3同步日志即可

2.1 写数据的流程
通过log队列异步写入
多数同意写入即可返回成功
排序/分组补偿丢失的数据
转化为data page
刷盘 gc 

2.2 高可用
通过s3冗余数据保证3个az总共有6份
通过多数同意的原则保证高可用和一致性

2.3 优势
通过s3保证了数据的容错/自愈/快速迁移
分离计算层可以简化计算、带动数据库计算层面的发展

3.2 通过cache接入到存储层 可以更加具有分布式的特性 
例如 锁在mysql里面是在内存中控制 其实是单机锁
但是在这里是通过log实现 也就是在存储层 更加具有分布式的特性

3.4 锁管理
细分锁的粒度
例如mysql insert delete scan的互斥关系
auroa通过lock manager细分锁粒度 提升性能

4 feature
Aurora目前支持mysql+ PostgreSQL 之后还可以支持更多的sql
更加契合云计算、分布式方向

link:https://open.toutiao.com/a6864848566217605636/?utm_medium=webview&vivo_news_comment_data=%7B%7D&vivo_news_source=1&vivo_news_comment_data_checksum=99914b932bd37a50b983c5e7c90ae93b&isNews=1&showOriginalComments=true&req_id=20201007200450010198058221405FA0C1&crypt=2342&item_id=6864848566217605636&showComments=0&gy=9cd782a0020b74f4199fc0ac066b62692025aa069a3534d14b2630fdb21988c0683da18fe372607e5ee20dfbc2cb2146f1ce98b7689e14bacd42acf96b72b0221960556acf487e50ea115933e9198bfd63607787b8169389e221861510e6112d56e68c9d321b23b8b4fea6e6ca48b9bba48e3474d8e42514aa1fc844de272a6eb6f9b1b1300a30a0c223c902ee6f0184&a_t=411603141796384875515377fd0&label=vivo_llq_channel&utm_source=vivoliulanqi&utm_campaign=open&dt=vivo%20X9i

sumary:
总的来说通过存储和计算的分离 提高的云服务的能力、3高等
通过例如锁的改进等提高性能
朝着万能数据库的方向发展

for me:
通过lock 细分提升性能 这个思路是比较可以实现的
