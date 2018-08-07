# 一条复杂sql语句的分析

## 1.功能需求  
### 1.1背景  
有两个表task和task_record，task表用来记录所有任务数据，其中包含一个字段是task_answer，没一个task在task_record表中有多条记录；task_record用来记录任务流水和详细信息，包括task_id用来对应某一条task，同时info字段用来记录流水完成任务时候的任务答案。  
### 1.2题目  
将task_record表中project为655的每条task数据最新一条记录的info作为答案写入task表中。  

## 2.解答  
### 2.1语句  
```sql
update task 
set task_answer=my.info 
from 
    (select task_id, info 
    from task_record 
    where project_id = 655 
    and id in (select MAX(id) 
                from task_record 
                where project_id = 655 
                group by task_id)) my
where task.id=my.task_id;
```

### 2.2分析
从内而外分析：  
##### 1.最内层  
```sql
select MAX(id) 
from task_record 
where project_id = 655 
group by task_id
```    
这条语句先根据task_id来分组，筛选project_id为655的所有task_record数据，然后获得其中最近一条数据(max(id))。注意，group by语句的使用中select的内容要么是group by的参数，要么是统计参数，例如count, max, min等。   
#### 2.倒数第二层  
```sql
select task_id, info 
from task_record 
where project_id = 655 
and id in (select MAX(id) 
           from task_record 
           where project_id = 655     
           group by task_id)
```  
为了和task表建立联系，接下来要查到最近一条数据的task_id和info信息，于是用了in语句来实现对id的限制，其他部分都是常规sql操作。  
#### 3.倒数第三层  
```sql
update task 
set task_answer=my.info 
from 
    (select task_id, info 
    from task_record 
    where project_id = 655 
    and id in (select MAX(id) 
                from task_record 
                where project_id = 655 
                group by task_id)) my
where task.id=my.task_id;
```  
有了和task关联的信息后，可以建立表的连接，task表和刚才查询出来的my表建立连接，连接点是task.id = my.task_id，然后update task表中task_answer字段为之前查出来的my.info。

