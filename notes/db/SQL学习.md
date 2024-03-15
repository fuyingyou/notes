## MySQL









## MongoDB



限制结果数量

```sql
.limit(1)
```

根据条件排序

```sql
.sort({send_date: -1})
```

执行详情

```sql
.explain('executionStats')
```

多条件取交集

```sql
{$and:[{},{}]}
```

存在某属性

```sql
{GPSX0:{$exists:true}}
```

指定字段是否显示(默认0，_id默认为1)

```sql
db.people.find(
    {},
    { user_id: 1, status: 1 }
) 
```



曾用命令记录

```sql
db.result_msgv1.find({GLIDERID_IRID:162}).sort({send_date: -1}).limit(1).explain('executionStats');

db.result_msgv1.find({$and:[{GLIDERID_IRID:162},{GPSX0:{$exists:true}},{GPSY0:{$exists:true}}]}).sort({send_date: -1}).limit(1).explain('executionStats');
```



