# Redis 事务定义
Redis 事务是一次可以执行相关联的一系列命令，是一组命令的集合，且事务中的命令有顺序。
事务有三个阶段：  
1.开始事务；  
2.命令入队；  
3.执行事务  

# Redis 事务特征
Redis 事务不具有隔离级别，exec 执行前，所有命令都在队列缓存中。 
和关系型数据库不同，缓存队列中的命令无法再变更。  
Redis 事务不保证原子性。命令格式如果有错误，则都不执行。如果格式都正确，就会依次执行，即使中间有命令执行失败，也不影响其他命令的执行，同时不会做事务回滚。  

# Redis 事务命令  
`watch key1 key2 ...` 监视一个或多个 KEY，如果事务执行时，KEY 的值被其他命令改动，则事务被打断，全部不执行，以此实现事务乐观锁；
`multi` 事务开始；  
`exec` 事务开始执行，当 exec 执行的时候监控所会被取消；  
`discard` 取消事务，放弃事务中所有命令；  
`unwatch` 取消对 watch 中 KEY 的监控  

# 实例
## 正常事务
```bash
multi
set key1 hello
set key2 my
set key3 world
get key2
exec
```  
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190717094305.png)  
multi 开始事务，直到 exec 执行的时候，所有在缓存队列里的事务被执行，没有命令格式错误，所以不会取消，也没有命令错误。  

## 取消事务  
```bash
multi
set key1 hello1
set key2 my1
set key3 world1
discard
get key2
``` 
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190717094546.png)  
multi 开始事务，discard 取消事务，所有事务中操作不生效。  

## 事务中命令错误  
```bash
multi
set key1 hello1
setok key2 my1
set key3 world1
get key3
exec
```  
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190717100810.png)  
multi 开始事务，setok key2 my1 命令出错，所有事务都不执行

## 事务中语法错误  
```bash
multi
incr key1
set key2 my1
set key3 world1
get key3
exec
```  
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190717100527.png)  
multi 事务开始，incr key1 语法错误，字符串无法自增，其他命令正确，事务正常执行完成，出错的命令抛错，但不影响其他命令执行  

## 事务中乐观锁  
```bash
set a 100
set b 20
watch a
multi
decrby a 50
incrby b 50
exec
get a
```  
a 账户 100，b 账户 20，a 向 b 转账 50，转账执行前 a 提现了 50，之后转账再获取 a 余额。  
![](https://hp-markdown-pic.oss-cn-beijing.aliyuncs.com/20190717101910.png) 
因为有 watch a，事务执行时发现 a 值改变，所以 exec 返回 nil，事务执行失败，此时查询 a, b 的余额，a 是提现后剩余的 50，b 是之前的 20。