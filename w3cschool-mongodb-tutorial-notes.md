# W3Cschool - MongoDB 教程笔记

教程：[W3Cschool - MongoDB 教程](<https://www.w3cschool.cn/mongodb/?>) 

[TOC]

## MongoDB 简介

**什么是 MongoDB ？**

MongoDB 是由C++语言编写的开源数据库系统。

在高负载的情况下，添加更多的节点，可以保证服务器性能。

MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，是一个基于分布式文件存储的数据库。

```
FirstName="Arun", Address="St. Xavier's Road", Spouse=[{Name:"Kiran"}], Children=[{Name:"Rihit", Age:8}]. 
FirstName="Sameer",Address="8 Gandhi Road". 
```

> 备注：以上数据有两个不同的文档（以"."分隔）。以这种方式存储数据即为文件存储的数据库。MongoDB是一个面向文档的数据库。

## MongoDB 概念解析

基本概念：文档、集合、数据库

一些概念：

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| ------------ | ---------------- | ----------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |

### 数据库

一个 MongoDB 可以建立多个数据库。

默认数据库为"db"。

MongoDB 的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

```javascript
--> 显示所有数据库列表
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
--> 显示当前数据库对象或集合
> db
admin
--> 连接指定数据库
> use admin
switched to db admin
--> 认证访问数据库
> db.auth('admin', '*********')
1
```

数据库命名规范：

- 不能是空字符串（"")。
- 不得含有 ' '（空格)  .  $  /  \ 和 \0（空宇符）。
- 应全部小写。
- 最多64字节。

保留数据库名：

- **admin**：从权限的角度来看，这是 "root" 数据库。要是将一个用户添加到这个数据库，用户将自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
- **local**：这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
- **config**：当 Mongo 用于分片设置时，"config" 数据库在内部使用，用于保存分片的相关信息。

### 文档

文档是一组键值对（key-value，即 BSON）。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型。这是 MongoDB 的突出特点。

简单示例：

```json
{ "site":"www.w3cschool.cn", "name":"W3Cschool教程" }
```

**需要注意的几点**

- 文档中的键/值对是有序的。

- 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档）。

- MongoDB 区分类型和大小写。

- MongoDB 的文档不能有重复的键。

- 文档的键是字符串。除了少数例外情况，键可以使用任意 UTF-8 字符。

**文档键命名规范**

- 键不能含有 \0（空字符）。这个字符用来表示键的结尾。
- . 和 $ 有特别的意义，只有在特定环境下才能使用。
- 以下划线 "_" 开头的键通常是保留的（不是严格要求）。

**关于键/值对有序的附加说明**

```javascript
--> 插入一条记录
> db.testcollection.insert({"type":"fruit", "detail":{"apple":2, "banana":3}});
--> 正确查询
> db.testcollection.find({"detail":{"apple":2,"banana":3}})
--> 错误查询
> db.testcollection.find({"detail":{"banana":3,"apple":2}})

--> 点符号操作
> db.testcollection.find({"detail.apple":2,"detail.banana":3})
> db.testcollection.find({"detail.banana":3，"detail.apple":2})
```

> 备注：查询具有嵌套文档的集合时需要注意有序，也可以考虑使用点符号操作。

### 集合

集合就是 MongoDB 文档组，类似于 RDBMS（Relational Database Management System，关系数据库管理系统）中的表格。

集合存在于数据库中，集合不要求有固定的数据结构。

当第一个文档插入时，集合就会被创建。

**集合命名规范**

- 集合名不能是空字符串 ""。
- 集合名不能含有 \0（空字符)，这个字符表示集合名的结尾。
- 集合名不能以 "system." 开头，这是为系统集合保留的前缀。
- 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现 $。

**Capped Collection**

Capped Collections 就是固定大小的 Collection。

有很高的性能以及队列过期的特性（过期按照插入的顺序）。

适合类似记录日志的功能。

必须要显式的创建一个 Capped Collection，指定一个 Collection 的大小，单位是字节。Collection 的数据存储空间值提前分配的。

注意指定的存储大小包含了数据库的头信息。

简单示例：

```javascript
db.createCollection("mycoll", {capped:true, size:100000})
```

- 在 Capped Collection 中，能添加新的对象。
- 能进行更新，然而，对象不会增加存储空间。如果增加，更新就会失败 。
- 数据库不允许进行删除。使用 `drop()` 方法删除 Collection 所有的行。
- 删除之后，必须显式的重新创建这个 Collection 。
- 在 32bit 机器中，Capped Collection 最大存储为 1e9 个字节。

### 元数据

数据库的信息是存储在集合中，使用了系统的命名空间：`dbname.system.*`。

数据库中命名空间 `<dbname>.system.*` 是包含多种系统信息的特殊集合，如下：

| 集合命名空间             | 描述                                      |
| ------------------------ | ----------------------------------------- |
| dbname.system.namespaces | 列出所有名字空间。                        |
| dbname.system.indexes    | 列出所有索引。                            |
| dbname.system.profile    | 包含数据库概要(profile)信息。             |
| dbname.system.users      | 列出所有可访问数据库的用户。              |
| dbname.local.sources     | 包含复制对端（slave）的服务器信息和状态。 |

修改系统集合对象的限制：

- 在 `{{system.indexes}}` 插入数据，可以创建索引。但除此之外该表信息是不可变的（特殊的 `drop index` 命令将自动更新相关信息）。
- `{{system.users}}` 是可修改的。`{{system.profile}}` 是可删除的。

**MongoDB 数据类型**

| 数据类型           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                            |
| Double             | 双精度浮点值。用于存储浮点值。                               |
| Min/Max keys       | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Arrays             | 用于将数组或列表或多个值存储为一个键。                       |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                       |
| Object             | 用于内嵌文档。                                               |
| Null               | 用于创建空值。                                               |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的 ID。                                 |
| Binary Data        | 二进制数据。用于存储二进制数据。                             |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                 |
| Regular expression | 正则表达式类型。用于存储正则表达式。                         |

## MongoDB 连接

**启动/停止/重启 MongoDB**

```shell
$ sudo systemctl start mongod
$ sudo systemctl stop mongod
$ sudo systemctl restart mongod
```

> 备注：
>
> 数据文件 - `/var/lib/mongodb/*`。
>
> 日志文件 - `/var/log/mongodb/mongod.log`。
>
> 配置文件 - `/etc/mongo.conf`。

**Shell 连接 MongoDB 服务**

```
$ mongo --port PORT -u USERNAME -p PASSWORD

--> 避免上述方式暴露密码，可以连接后进行认证
$ mongo --port PORT
MongoDB shell version v4.0.5
connecting to: mongodb://127.0.0.1:32937/?gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("aa4b9f06-a199-4833-9b12-4bbf6940c68f") }
MongoDB server version: 4.0.5
> use DBNAME
switched to db DBNAME
> db.auth(USERNAME, PASSWORD)
1
```

## MongoDB 基本操作

### 创建数据库

**语法**

```
use DATABASE
```

> 备注：若数据库不存在则创建，存在则切换到指定数据库

**实例**

```javascript
> use mydb
switched to db mydb
> db
mydb
> show dbs
config  0.000GB
local   0.000GB
> db.mydb.insert({"name":"mydb"})
WriteResult({ "nInserted" : 1 })
> show dbs
config  0.000GB
local   0.000GB
mydb    0.000GB
```

> 备注：数据库创建后使用 `show dbs` 命令并不能查询到，需要先插入一些数据。

### 删除数据库

**语法**

```
db.dropDatabase()
```

> 备注：默认数据库是 `test`，可以用 `db` 命令查看当前数据库名

**实例**

```javascript
> show dbs
config  0.000GB
local   0.000GB
mydb    0.000GB
> use mydb
switched to db mydb
> db.dropDatabase()
{ "dropped" : "mydb", "ok" : 1 }
> show dbs
config  0.000GB
local   0.000GB
```

**删除集合**

```
db.collection.drop()
```

### 插入文档

文档的数据结构与 JSON 基本一样。

存储在集合中的数据都是 BSON 格式。

BSON 是一种类 JSON 的二进制形式的存储格式，简称 Binary JSON。

**语法**

```
db.COLLECTION.insert(document)
```

**实例**

```javascript
> db.mycol.insert({title: 'MongoDB 教程', 
...     description: 'MongoDB 是一个 Nosql 数据库',
...     by: 'w3cschool',
...     url: 'http://www.w3cschool.cn',
...     tags: ['mongodb', 'database', 'NoSQL'],
...     likes: 100
... })
WriteResult({ "nInserted" : 1 })
> db.mycol.find()
{ "_id" : ObjectId("5d4cdd49633099384023ae75"), "name" : "nogg" }
{ "_id" : ObjectId("5d4cddfa633099384023ae76"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "w3cschool", "url" : "http://www.w3cschool.cn", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
```

> 备注：若集合 `mycol` 不存在则自动创建并插入文档。

也可以定义为变量，然后进行插入操作：

```javascript
> document=({title: 'MongoDB 教程', 
...     description: 'MongoDB 是一个 Nosql 数据库',
...     by: 'w3cschool',
...     url: 'http://www.w3cschool.cn',
...     tags: ['mongodb', 'database', 'NoSQL'],
...     likes: 100
... });
{
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "w3cschool",
        "url" : "http://www.w3cschool.cn",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
> db.mycol.insert(document)
WriteResult({ "nInserted" : 1 })
```

插入文档也可以使用 `db.mycol.save(document)` 命令。如果不指定 `_id` 字段 `save()` 方法类似于 `insert()` 方法。如果指定 `_id` 字段，则会更新该 `_id` 的数据。

### 更新文档

MongoDB 使用 `update()` 函数更新数据。

**语法**

```
db.COLLECTION.update(criteria, objNew, upsert, multi)
```

参数说明：

- **criteria**：查询条件，类似 sql update 查询 where 后面的内容。
- **objNew**：更新的对象和一些更新的操作符（如 `$set`、`$inc`）等，类似 sql update 查询 set 后面的内容。
- **upsert**：如果不存在符合条件的记录，是否插入 objNew。默认是 false，不插入。
- **multi**：默认是 false，只更新查询到的第一条记录，如果是 true，就将按条件查询到的多条记录全部更新。

**实例**

```javascript
> db.test.update({"count":{$gt:3}}, {$set:{"status":"OK"}});
> db.test.update({"count":{$gt:3}}, {$set:{"status":"OK"}}, false, true);
> db.test.update({"count":{$gt:3}}, {$set:{"status":"OK"}}, true, false);
> db.test.update({"count":{$gt:3}}, {$set:{"status":"OK"}}, true, true);
> db.test.update({"count":{$gt:3}}, {$inc:{"status":"OK"}}, false, false);
> db.test.update({"count":{$gt:3}}, {$inc:{"status":"OK"}}, false, true);
```

### 删除文档

**语法**

```
db.COLLECTION.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```

参数说明：

- **query**：删除的文档的条件。（可选）
- **justOne**：如果为 true，则只删除一个文档。（可选）
- **writeConcern**：抛出异常的级别。（可选）

**实例**

```javascript
> db.mycol.find()
{ "_id" : ObjectId("5d4ced02633099384023ae7a"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "w3cschool", "url" : "http://www.w3cschool.cn", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
> db.mycol.remove({'title':'MongoDB 教程'})
WriteResult({ "nRemoved" : 1 })
```

删除全部数据：

```javascript
> db.mycol.remove({})
WriteResult({ "nRemoved" : 1 })
> db.mycol.find()
> 
```

### 查询文档

**语法**

```
db.COLLECTION.find(QUERY, PROJECTION)
db.COLLECTION.find().pretty() // 易读格式
db.COLLECTION.findOne() // 只返回一个文档
```

参数说明：

- QUERY：查询条件。
- PROJECTION：指定键的显示与否（0 - 不显示，1 - 显示）。

> 备注：`find()` 方法以非结构化的方式来显示所有文档，`pretty()` 方法以格式化的方式来显示所有文档。

**实例**

```javascript
> db.mycol.find()
{ "_id" : ObjectId("5d4cf0ad633099384023ae7d"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "w3cschool", "url" : "http://www.w3cschool.cn", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
> db.mycol.find().pretty()
{
        "_id" : ObjectId("5d4cf0ad633099384023ae7d"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "w3cschool",
        "url" : "http://www.w3cschool.cn",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
> db.mycol.find({}, {"title":1, "by":1, _id:0})
{ "title" : "MongoDB 教程", "by" : "w3cschool" }
```

**MongoDB 与 RDBMS Where 语句对比**

| 操作     | 格式                     | 范例                                        | RDBMS类似语句            |
| -------- | ------------------------ | ------------------------------------------- | ------------------------ |
| 等于     | `{<key>:<value>`}        | `db.col.find({"by":"w3cschool"}).pretty()`  | `where by = 'w3cschool'` |
| 小于     | `{<key>:{$lt:<value>}}`  | `db.col.find({"likes":{$lt:50}}).pretty()`  | `where likes < 50`       |
| 小于等于 | `{<key>:{$lte:<value>}}` | `db.col.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50`      |
| 大于     | `{<key>:{$gt:<value>}}`  | `db.col.find({"likes":{$gt:50}}).pretty()`  | `where likes > 50`       |
| 大于等于 | `{<key>:{$gte:<value>}}` | `db.col.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50`      |
| 不等于   | `{<key>:{$ne:<value>}}`  | `db.col.find({"likes":{$ne:50}}).pretty()`  | `where likes != 50`      |

**MongoDB AND 条件**

```
db.COLLECTION.find({key1:value1, key2:value2}).pretty()
```

**MongoDB OR 条件**

```
db.COLLECTION.find({
  $or: [
     {key1: value1}, {key2:value2}
  ]
}).pretty()
```

**查询嵌套文档附加说明**

要在嵌入/嵌套文档中的字段上指定查询条件，请使用点符号(”`field.nestedField`“)。

```javascript
> db.inventory.find({"size.height":{$lt:15}})
```

### 条件操作符

**比较操作符**

MongoDB 中比较操作符有：

- （>）大于 - `$gt`
- （<）小于 - `$lt`
- （>=）大于等于 - `$gte`
- （<=）小于等于 - `$lte`

**$type 操作符**

`$type` 操作符是基于 BSON 类型来检索集合中匹配数据类型的文档，并返回结果。

MongoDB 中可以使用的类型：

| **类型**                | **数字** | **备注** |
| ----------------------- | -------- | -------- |
| Double                  | 1        |          |
| String                  | 2        |          |
| Object                  | 3        |          |
| Array                   | 4        |          |
| Binary data             | 5        |          |
| Undefined               | 6        | 已废弃。 |
| Object id               | 7        |          |
| Boolean                 | 8        |          |
| Date                    | 9        |          |
| Null                    | 10       |          |
| Regular Expression      | 11       |          |
| JavaScript              | 13       |          |
| Symbol                  | 14       |          |
| JavaScript (with scope) | 15       |          |
| 32-bit integer          | 16       |          |
| Timestamp               | 17       |          |
| 64-bit integer          | 18       |          |
| Min key                 | 255      | -1       |
| Max key                 | 127      |          |

**实例**

```javascript
> db.mycol.find({"title":{$type:2}})
```

### Limit 与 Skip 方法

**limit 方法**

```
db.COLLECTION.find().limit(NUMBER)
```

> 备注：读取指定数量的数据记录。

**skip 方法**

```
db.COLLECTION.find().limit(NUMBER).skip(NUMBER)
```

> 备注：跳过指定数量的数据记录。默认参数为 0。

### 排序

MongoDB 使用 `sort()` 方法对数据进行排序。

**`sort()` 语法**

```
db.COLLECTION.find().sort({KEY:FLAG})
```

参数说明：

- KEY：指定排序依据的字段/键
- FLAG：指定排序方式。1 为升序排列，-1 为降序排列。默认为升序。

**实例**

```javascript
> db.mycol.find({}, {"title":1, _id:0}).sort({"title":-1})
{ "title" : "PHP 教程" }
{ "title" : "MongoDB 教程" }
{ "title" : "Java 教程" }
```

### 索引

索引通常能够极大的提高查询的效率。

索引是特殊的数据结构，存储在一个易于遍历读取的数据集合中，是对数据库表中一列或多列的值进行排序的一种结构。

MongoDB 使用 `ensureIndex()` 方法来创建索引。

**`ensureIndex()` 语法**

```
db.COLLECTION_NAME.ensureIndex({KEY1:FLAG1, KEY2:FLAG2, ...})
```

接收可选参数：

| 参数               | l类型         | 描述                                                         |
| :----------------- | :------------ | :----------------------------------------------------------- |
| background         | Boolean       | 建索引过程会阻塞其它数据库操作，参数 “background” 可指定以后台方式创建索引。 默认值为 **false。** |
| unique             | Boolean       | 建立的索引是否唯一。指定为 true 创建唯一索引。默认值为 **false。** |
| name               | string        | 索引的名称。如果未指定，自动通过连接索引的字段名和排序顺序生成索引名称。 |
| dropDups           | Boolean       | 在建立唯一索引时是否删除重复记录。默认值为 **false。**       |
| sparse             | Boolean       | 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为 true 的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 **false**。 |
| expireAfterSeconds | integer       | 指定一个以秒为单位的数值，完成 TTL 设定，设定集合的生存时间。 |
| v                  | index version | 索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。 |
| weights            | document      | 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。 |
| default_language   | string        | 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语。 |
| language_override  | string        | 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language。 |

**实例**

```javascript
> db.mycol.ensureIndex({"title":1, "description":-1}, {background:true})
```

**索引使用附加说明**

[MongoDB 索引使用](<https://www.cnblogs.com/jiqing9006/p/7866150.html>) 

### 聚合

MongoDB 中聚合（`aggregate()`）主要用于处理数据（诸如统计平均值、求和等），并返回计算后的数据结果。类似 sql 语句中的 `count()`。

**语法**

```
db.COLLECTION.aggregate(AGGREGATE_OPERATION)
```

**实例**

```javascript
> db.article.aggregate([{$group:{_id:"$by_user", num_tutorial:{$sum:1}}}])
{ "_id" : "Neo4j", "num_tutorial" : 1 }
{ "_id" : "w3cschool.cn", "num_tutorial" : 2 }
```

> 备注：以上实例通过字段 `by_user` 字段对数据进行分组，并计算 `by_user` 字段相同值的总和。该命令类似于 sql 语句 `select by_user, count(*) from articles group by by_user`。

**一些聚合表达式**

| 表达式    | 描述                                         | 实例                                                         |
| :-------- | :------------------------------------------- | :----------------------------------------------------------- |
| $sum      | 计算总和                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
| $avg      | 计算平均值                                   | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
| $min      | 获取集合中所有文档对应值得最小值             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
| $max      | 获取集合中所有文档对应值得最大值             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
| $push     | 在结果文档中插入值到一个数组中               | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}]) |
| $addToSet | 在结果文档中插入值到一个数组中，但不创建副本 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
| $first    | 根据资源文档的排序获取第一个文档数据         | db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
| $last     | 根据资源文档的排序获取最后一个文档数据       | db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}]) |

**管道的概念**

管道在 Unix 和 Linux 中一般用于将当前命令的输出结果作为下一个命令的参数。

MongoDB 的聚合管道将 MongoDB 文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

**聚合框架常用操作**

- `$project`：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
- `$match`：用于过滤数据，只输出符合条件的文档。`$match` 使用 MongoDB的标准查询操作。
- `$limit`：用来限制 MongoDB 聚合管道返回的文档数。
- `$skip`：在聚合管道中跳过指定数量的文档，并返回余下的文档。
- `$unwind`：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
- `$group`：将集合中的文档分组，可用于统计结果。
- `$sort`：将输入文档排序后输出。
- `$geoNear`：输出接近某一地理位置的有序文档。

**实例**

```javascript
--> $project 实例
> db.article.aggregate({
  $project: {
    _id: 0,
    title: 1,
    author: 1,
  }
});
```

> 备注：除了 `title` 和 `author`，其他域被“过滤”掉。

```javascript
--> $match 实例
> db.article.aggregate([
  {$match: {score: {$gt: 70, $lte: 90}}},
  {$group: {_id: null, count: {$sum: 1}}}
]);
```

> 备注：`$match` 用于获取分数大于 70 小于等于 90 的记录，然后将符合条件的记录送到下一阶段 `$group` 管道操作符进行处理。

```javascript
--> $skip 实例
> db.article.aggregate({$skip: 5});
```

> 备注：经过 `$skip` 管道操作符处理后，前五个文档被跳过。

### 复制（副本集）

[MongoDB 复制（副本集）](<https://www.w3cschool.cn/mongodb/mongodb-replication.html>) 

### 分片

[MongoDB 分片](<https://www.w3cschool.cn/mongodb/mongodb-sharding.html>) 

## MongoDB 进阶