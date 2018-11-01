## 0.Fun
> 有日志的TDD是test driven development；
没有日志的TDD是tragedy driven development。


## 1.简介
日志用来做记录，日志记录涉及networks, servers, containers, cloud infrastructure, applications, application infrastructure, message bus(kafka), load balancers等。一般的管理流程如下，通常由DevOpers完成：
1. instruct and collect
2. centralize and index
3. serch and analyze
4. monitor and alert
5. report and dashboard

好的日志可以帮助：
1. 了解线上系统的运行状态
2. 快速准确定位线上问题
3. 发现系统瓶颈
4. 预警系统潜在风险
5. 挖掘产品最大价值
6. ...

## 2.分类
本文关注applications的日志管理，从记录内容区分通常包括两种：一种是异常日志，另一种是业务日志。
* 异常日志
服务运行时会出现两种异常，一种是没有被预期到抛出的异常，另一种是被预期到捕获的异常。
前者通过异常管理工具控制，例如Sentry，在服务初始化的时候设置SENTRY\_DSN(data source name)和SENTRY\_ENVIRONMENT，项目运行时如果出现了被抛出的异常则会通过rpc发送给相应的dsn，在控制台可以看到相应情况，成熟的管理工具可以看到清晰的异常上下文，帮助记录，同时方便debug。
![](http://p27x0f47q.bkt.clouddn.com/20181101170157.png)
![](http://p27x0f47q.bkt.clouddn.com/20181101170331.png)
后者又有两种常见模式，一种是后端管理UI异常显示，另一种是前端管理UI异常显示。前端管理的异常显示后端比较自由，可以将异常上下文返回给前端，好处是方便在浏览器控制台快速调试，坏处是信息显示在前端不安全且传输量可能比较大。更常见的是后端管理的异常显示，将UI需要显示的信息返回给前端，将上下文的信息记录在日志中，方便后续管理和debug。
* 业务日志
服务运行时往往需要记录非异常数据，例如对一些节点的计数，访问信息的记录和分析。

从功能上来说，日志通常包括诊断日志、统计日志、审计日志。
* 诊断日志
请求入口和出口
外部服务调用和返回
资源消耗操作: 如读写文件等
容错行为： 如云硬盘的副本修复操作
程序异常： 如数据库无法连接
后台操作：定期执行删除的线程
启动、关闭、配置加载
* 统计日志
用户访问统计：用户IP、上传下载的数据量，请求耗时等
计费日志（如记录用户使用的网络资源或磁盘占用，格式较为严格，便于统计）
* 审计日志
管理操作

## 3.内容
记录原则：
1. 相关日志应该在一条中记录，而不是拆分成多条；
2. 只输出需要的日志，过滤不需要关注的信息；
3. 能反映debug需要的充要信息，例如系统配置信息，数据结构，异常类型，报错信息，系统执行时间等

记录示范：
```shell
[INFO] RequestID:b1946ac92492d2347c6235b4d2611184, auth failed due to token expiration
[INFO] RequestID:b1946ac92492d2347c6235b4d2611185, content digest does not match, expect 7b3f050bfa060b86ba781151c563c953, actual f60645e7107917250a6408f2f302d048
[INFO] RequestID:b1946ac92492d2347c6235b4d2611186, request ip(=202.17.34.1) not in whitelist
```
结构制定：
```shell
[year-month-day hour:min:sec <IP>] [<LEVEL>]: <ENV>, <RequestID>, <error_msg>, <PARAMS>
```


## 4.级别
常用级别，低到高：
* TRACE – The TRACE Level designates finer-grained informational events than the DEBUG
* DEBUG – The DEBUG Level designates fine-grained informational events that are most useful to debug an application.
* INFO – The INFO level designates informational messages that highlight the progress of the application at coarse-grained level.
* WARN – The WARN level designates potentially harmful situations.
* ERROR – The ERROR level designates error events that might still allow the application to continue running.
* FATAL – The FATAL level designates very severe error events that will presumably lead the application to abort.

DEBUG/TRACE: 记录新功能开发的核心节点日志，用于确保功能开发的切实落地；
INFO：记录子系统初始化，核心请求正常执行，用于查询更高级别日志的上下文；
WARN：记录系统可能出现的问题，例如网络波动，硬盘空间不足等，不需要立即处理，但要及时维护；
ERROR：影响个别用户访问的问题，需要立即处理；
FATAL：影响大范围用户访问的问题，需要立即处理，谨慎使用，设置这个级别的日志一定数量一定要控制，已有人力能够立即修复。

## 5.评估
* 根据问题出现后的定位速度评估日志系统的优劣；
* 根据日志分析得出的系统瓶颈和开发指导来评估日至系统的优劣；
* 根据高级别日志出现的频率来评估日志系统的优劣；
* 根据运维和开发人员沟通的情况来评估日至系统的优劣。

> 日志是一个容易被忽略的管理内容，一个好的团队会重视日志的管理，定期优化。日志是开发和运维质量的试纸。

Reference: 
http://blog.jobbole.com/56574/