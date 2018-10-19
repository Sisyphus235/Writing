## Intro
protocol buffers主要用来同步数据结构，避免数据结构造成的多方数据结构调整问题，基于pre-defined data structure生成各方需要的codings，规定内容存储在.proto中。
start with an example
```proto
// 如果不定义proto3，默认使用proto2，且一定要在第一行定义
syntax = "proto3"; 

# SearchRequest相当于一个class
message SearchRequest {  
    // 每个filed包含两部分，name和type，数字1在encode时使用，1-15是一个字节，所以1-15的数字都是高频filed
    string query = 1;  
    int32 page_number = 2;
    int32 result_per_page = 3;
}
```

## Filed Type
built-in类型包括int32, int64, unit32, unit64, sint32等，参见
(https://developers.google.com/protocol-buffers/docs/proto3#scalar)；
自定义类型，如下例
```
enum EnumAllowingAlias{
    option allow_alias = true;
    UNKNOWN = 0;
    STARTED = 1;
    RUNNING = 1;
}
```
(https://developers.google.com/protocol-buffers/docs/proto3#enum)
**定义enum的时候要注意，一定第一个常数是0**

## Assigning Field Numbers
每个field的number是unique的，用来唯一识别field，当定义的class运行后不能再改变。number的range是1到2^29 - 1，除去19000-19999。高频的field分配1-15，只用1个字节。

## Practice
|area|function|protobuf_2|protobuf_3|
|-|-|-|-|
|field|initiation|instance()|the same|
|filed|add|setattr(instance, \<field_name>, \<value>)|the same|
|field|view|getattr(instance, \<field_name>)|the same|
|fileld|copy|instance.\_\_deepcopy__()|the same|
|field|del|instance.ClearField(\<field_name>)|the same|
|subfield|field_name|[field.name for field in \<model>.DESCRIPTOR.fields]|the same|
|subfield|length|len(instance.\<subfiled_name>)|the same|
|subfield|add|instance.\<subfiled_name>.extend([instance2, instance3, ...])|the same|
|subfield|view|getattr(instance, \<field_name>)[0]|the same|
|subfield|copy|instance.\_\_deepcopy__()|the same|
|subfield|del|instance.\<subfiled_name>.ClearField(\<field_name>)|the same|

```python
from shared_models.generated.python import task_pb2_pb2
job1 = task_pb2_pb2.Job(id=1, question='hi1')
point_tool = task_pb2_pb2.PointTool(show_digit=False, show_line=True)
job1.tool.Pack(point_tool)
job2 = job1.__deepcopy__()
setattr(job2, 'id', 2)
setattr(job2, 'question', 'hi2')
job1.subJobs.extend([job2])
```