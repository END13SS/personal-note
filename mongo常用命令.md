## MongoDB常用命令
连接数据库：`mongodb://admin:123456@localhost/test`

创建数据库：`use DATABASE_NAME`

查看所有数据库：`show dbs`

**新创建的数据库中若没有插入数据，则不会显示在列表中**

数据库中插入数据：`db.collection.insert`

删除数据库：`db.dropDatabase()`

创建集合：`db.createCollection(name, options)`

查看已有集合：`show collections / show tables`

删除集合：`db.collection.drop()`

**文档操作（以test数据库举例）**

**插入文档：**

```bash
db.test.insert({title: 'MongoDB',
description: 'Nosql数据库',
by: 'ssj',
url: '[http://www.baidu.com](http://www.baidu.com/)',
tags: ['mongodb', 'database', 'NoSQL'],
likes: 100})
```

查看已插入文档：

```bash
db.test.find(){ "_id" : ObjectId("56064886ade2f21f36b03134"), "title" : "MongoDB", "description" : "Nosql数据库", "by" : "ssj", "url" : "http://www.baidu.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
```

update方法（用于更新已存在的文档）

`db.test.update({'title':'MongoDB'},{$set:{'title':'Mongo'}})`

查看更新后文档：

`db.test.find().pretty()`

结果：

```bash
{
        "_id" : ObjectId("56064f89ade2f21f36b03136"),
        "title" : "Mongo",
        "description" : "Nosql数据库",
        "by" : "ssj",
        "url" : "http://www.baidu.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100}
```

save方法（通过传入的文档来替换已有的文档，id主键存在就更新，不存在就插入）

```bash
db.test.save({
    "_id" : ObjectId("56064f89ade2f21f36b03136"),
    "title" : "Mongo",
    "description" : "Nosql数据库",
    "by" : "ssj",
    "url" : "http://www.baidu.com",
    "tags" : [
            "mongodb",
            "NoSQL"
    ],
    "likes" : 110})
```

**删除文档：**

`db.test.remove({'title':'MongoDB'})`

**查询文档（格式化）：**

`db.test.find().pretty()`

AND条件：

`db.test.find({key1:value1, key2:value2}).pretty()`

OR条件：

`db.test.find(`

`{`

`$or: [`

`{key1: value1}, {key2:value2}`

`]`

`}).pretty()`

**AND和OR联合使用**

类似常规 SQL 语句为： `'where likes>50 AND (by = 'ssj' OR title = 'MongoDB')'`

`db.test.find({"likes": {$gt:50}, $or: [{"by": "ssj"},{"title": "MongoDB"}]}).pretty()`

结果：
```{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB",
        "description" : "Nosql",
        "by" : "ssj",
        "url" : "http://www.baidu.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100}
```
