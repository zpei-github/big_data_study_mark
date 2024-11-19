## 1. 【SQL】使用concat函数进行多字段统计的问题

[Mysql解决concat中字段为null的问题（concat_ws）_concat为null-CSDN博客](https://blog.csdn.net/wang1qqqq/article/details/106497597)

```sql
select 
count(*), 
count(distinct concat(字段1, 字段2, 字段3) ) 
from 
    table1;
```

在使用上述代码来统计表中联合字段主键的唯一性时出现异常，count(*)不等于count(distinct concat(字段1, 字段2, 字段3))的结果，则存在两个问题:

1、表的联合主键<字段1, 字段2, 字段3>不唯一

2、联合主键<字段1, 字段2, 字段3>中存在字段为null

`concat(字段1, 字段2, 字段3,....)`函数存在特性：当任意字段值为null，则拼接结果为null

所以想要更好的统计联合主键唯一值时，可以使用`concat_ws([分隔符], 字段1, 字段2, 字段3,....)`该函数可以排除null值，实现全拼接。所以代码改为: 

```sql
select 
count(*), 
count(distinct concat_ws("-",字段1, 字段2, 字段3) ) 
from 
    table1;
```

## 2. 【Mongo】使用MongoDB数据库的坑--Mongo库中存储数据会携带类型

[mongodb采坑记录-number/numberlong_mongodb numberlong-CSDN博客](https://blog.csdn.net/Himly_Zhang/article/details/104967146)

MongoDB中存储的数据，比如超长整数据，会在储存时标记类型。但是在数据库客户端则会将数据类型解析，返回数据。

```javascript
> use  testDB

> db.testColl.findOne()
{
        "_id" : ObjectId("63ec79364610bf82b99d23a5"),
        "pid" : "XT-db238f62-cc77-40bb-8952-9c96544dc786",
        "group" : "全部订阅用户",
        "groupUserId" : "63c0ba6de4b095b2050d9f0a",
        "type" : 1,
        "eid" : "123456",
        "createTime" : NumberLong("1676441910831"),
        "updateTime" : NumberLong("1676441910831")
}
```

上面是储存在Mongo中的源数据，但是如DataGrip等软件会将createTime中的NumberLong()去掉，只显示内部数据1676441910831




因此在同步来自Mongo数据库的数据时，需要注意数据中携带的数据类型，在进行清洗时要进行替换。



## 3. 【Hive】Hive中两张表关联时，以两个同名字段作为关联条件时报错的坑

[SemanticException Column xx Found in more than One Tables/Subqueries_column found in more than one-CSDN博客](https://blog.csdn.net/qq_35078688/article/details/86521578)



进行两张表的关联时，建议一定要用关联条件进行关联，这几乎是所有公司的SQL规范。在Hive中也是如此，但是Hive中的表关联条件却有一些坑:





## 4. 【Hive】Hive 转义字符

[HIVE 转义字符与特殊字符处理_hive特殊字符转义-CSDN博客](https://blog.csdn.net/liuya19921123/article/details/120367193)

Hive中对字符串的替换和切割是一个十分频繁的操作，但是针对特殊字符的处理却是Hive中的一个大坑。



在Hive中对特殊字符比如-"\$&^@_进行正则替换和正则切割时，需要使用两个转义字符。例如以"\$"作为分隔符，需要对字符串进行切割时，就需要split("aaa\$bbb", "\\\\$")。原因是在字符串中，单个反斜杠被用作转义字符，所以在构造正则表达式的字符串中，需要两个反斜杠来表示正则表达式中的一个反斜杠。


## 5. 【Hive】 lateral view的性能损耗
[你真的了解Lateral View explode吗？--源码复盘](https://zhuanlan.zhihu.com/p/137482744)

