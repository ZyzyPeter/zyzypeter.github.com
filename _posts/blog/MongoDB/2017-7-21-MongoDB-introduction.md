---
layout: post
title: MongoDB的概念解析
categories: MongoDB
description: 数据库
keywords: sql, nosql, mongodb
---

先来看看MongoDB与sql的常用术语与概念对比

| SQL术语/概念 | MongoDb术语/概念 |解释 （SQL/MongoDB）|
|--------|--------|---------|
|database|database|数据库|
|table|collection|数据库表/集合|
|row|document|数据记录行/文档|
|column|field|	数据字段/域|
|index	|index|	索引|
|table joins|	 	|表连接,MongoDB不支持|
|primary key|	primary key|	主键,MongoDB自动将_id字段设置为主键|
|mysqld|mongod|数据库服务|
|mysql|mongo|客户端|

## 数据库（database）

一个mongodb中可以建立多个数据库。
MongoDB的默认数据库为“db”，该数据库存储在data目录中。
MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

我们可以用“**show dbs**”命令显示所有数据的列表
```
> show dbs
admin  0.000GB
local  0.000GB
test   0.000GB
```

执行“**db**”命令以显示当前数据库对象和集合
```
> db
test
```

运行“**use**”命令可以连接到一个指定的数据库
```
> use local
switched to db local
```

数据库也通过名字来标识。数据库名可以是满足以下条件的任意UTF-8字符串。
* 不能是空字符串（"")。
* 不得含有' '（空格)、.、$、/、\和\0 (空字符)。
* 应全部小写。
* 最多64字节。

有一些数据库名称是保留的，这些数据库有特殊的作用，允许直接访问。
* **admin**：权限上类似于"root"数据库。当我们将一个用户添加到这个数据库的时候，这个用户将会自动继承所有数据库的权限。一些特定的服务器端的命令也只能从该数据库运行。
* **local**
* **config**

## 文档（documentary）

文档是一组键值（key-value）对。MongoDB的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别。
一个简单的文档例子如下：
```
{"url":"www.baidu.com","name":"baidu"}
```
可以发现，与python中的dict类型很像。

文档有以下几个特点：
1. 文档中的键值对是**有序**的
2. 文档中的值的类型不仅可以是包裹在双引号内的字符串，还可以是其它的类型（甚至是整个嵌入的文档）
3. MongoDB**区分类型**和**大小写**
4. MongoDB的文档中**不能够有重复的键**
5. 文档的**键的类型是字符串**。除了少数例外情况，键可以使用任意UTF-8字符

**文档键的命名规范**：
* 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
* .和$有特别的意义，只有在特定环境下才能使用。
* 以下划线"_"开头的键是保留的(不是严格要求的)。

## 集合

集合就是一组MongoDB文档，类似于关系型数据库中的表格。
集合存在于数据库中，集合没有固定的结构，这意味着我们可以对集合插入不同类型和格式的数据，但通常情况下，同一集合内包含的数据总是有关联的。
不同的数据结构可以被放在同一个集合内：

```
{"class":"1"}
{"class":"2","number":"1"}
{"name":"gg"}
```

**合法的集合名**：

* 集合名不能是空字符串""。
* 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
* 集合名不能以"system."开头，这是为系统集合保留的前缀。
* 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。

## Mongo DB的数据类型

以下为MongoDB中常用的数据类型：

| 数据类型 | 描述 |
|--------|--------|
|String|	字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。|
|Integer|	整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。|
|Boolean|	布尔值。用于存储布尔值（真/假）。|
|Double|	双精度浮点值。用于存储浮点值。|
|Min/Max keys|	将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。|
|Arrays|	用于将数组或列表或多个值存储为一个键。|
|Timestamp|	时间戳。记录文档修改或添加的具体时间。|
|Object|	用于内嵌文档。|
|Null|	用于创建空值。|
|Symbol	|符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。|
|Date	|日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。|
|Object ID|	对象 ID。用于创建文档的 ID。|
|Binary Data|	二进制数据。用于存储二进制数据。|
|Code	|代码类型。用于在文档中存储 JavaScript 代码。|
|Regular expression|	正则表达式类型。用于存储正则表达式。|