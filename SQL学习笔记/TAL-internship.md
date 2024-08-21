## 使用concat函数进行多字段统计的问题

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
