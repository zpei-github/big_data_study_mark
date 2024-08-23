## 使用concat函数进行多字段统计的问题

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



## 使用MongoDB数据库的坑--Mongo库中存储数据会携带类型

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
