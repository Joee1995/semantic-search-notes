# W3Cschool - Neo4j 教程笔记

教程：[W3Cschool - Neo4j 教程](https://www.w3cschool.cn/neo4j/) 

[TOC]

## Neo4j 入门

### Neo4j 简介

**什么是 Neo4j？**

Neo4j 是一个 开源、无 Schema、无 SQL的图形数据库，图形数据库也称为图形数据库管理系统或 GDBMS。[官方网站](http://www.neo4j.org/) 

**图形数据库**是以图形结构的形式存储数据的数据库。 它以节点，关系和属性的形式存储应用程序的数据。 正如 RDBMS 以表的“行，列”的形式存储数据，GDBMS 以“图形”的形式存储数据。

**图形**是一组节点和连接这些节点的关系。 节点和关系包含表示数据的属性。 属性是用于表示数据的键值对。

### Neo4j 数据模型

Neo4j 图数据库遵循属性图模型来存储和管理其数据。

属性图模型规则：

- 表示节点、关系和属性中的数据
- 节点和关系都包含属性
- 关系连接节点
- 属性是键值对
- 节点用圆圈表示，关系用方向箭头表示。
- 关系具有方向：单向和双向。
- 每个关系包含“开始节点/从节点”和“到节点/结束节点”

在属性图数据模型中，关系应该是定向的。

图形数据库数据模型的主要构建块：**节点**、**关系**和**属性**。

### Neo4j 构建模块

Neo4j 图数据库的主要构建块：

- 节点：图表的基本单位，包含具有键值对的属性
- 属性：用于描述图节点和关系的键值对
- 关系：图形数据库的另一个主要构建块。，它连接两个节点
- 标签：将一个公共名称与一组节点或关系相关联，节点或关系可以包含一个或多个标签
- 数据浏览器：用于执行 CQL 命令并查看输出输出（可将结果导出为 CSV / JSON）

### Neo4j 安装

以 Ubuntu 18.04 为例。

下载并解压 `.tar.gz` 包：

```shell
$ wget https://neo4j.com/artifact.php?name=neo4j-community-3.5.8-unix.tar.gz
$ tar -zxvf neo4j-community-3.5.8-unix.tar.gz
$ mv neo4j-community-3.5.9-unix /usr/local/neo4j-3.5.8
```

解压后目录结构如下：

```
.
|--- bin/
|--- certificates/
|--- conf/
|--- data/
|--- import/
|--- lib/
|--- logs/
|--- plugins/
|--- run/
|--- LICENSES.txt
|--- LICENSE.txt
|--- NOTICE.txt
|--- README.txt
|--- UPGRADE.txt
```

在 `~/.bashrc` 的末尾添加如下行配置环境变量：

```
# set neo4j environment
export NEO4J_HOME="/usr/local/neo4j-3.5.8"
export PATH="$NEO4J_HOME/bin:$PATH"
```

编辑 `conf/neo4j.conf` 文件修改配置（可选）。详见[Neo4j 配置文件](./neo4j-conf-detailed.md)。

启动/停止/重启 Neo4j 服务器：

```shell
$ sudo neo4j start
$ sudo neo4j stop
$ sudo neo4j restart
```

浏览器访问 `http://localhost:7474`。

## Neo4j CQL

### Neo4j CQL 简介

**什么是 CQL？**

- Neo4j 图形数据库的查询语言
- 一种声明性模式匹配语言
- 遵循 SQL 语法
- 语法是非常简单、人性化、可读的格式

**CQL 命令/条款**

| 命令/条款        | 用法                         |
| ---------------- | ---------------------------- |
| CREATE 创建      | 创建节点，关系和属性         |
| MATCH 匹配       | 检索有关节点，关系和属性数据 |
| RETURN 返回      | 返回查询结果                 |
| WHERE 哪里       | 提供条件过滤检索数据         |
| DELETE 删除      | 删除节点和关系               |
| REMOVE 移除      | 删除节点和关系的属性         |
| ORDER BY 以…排序 | 排序检索数据                 |
| SET 组           | 添加或更新标签               |

**Neo4j 函数**

| 定制列表功能      | 用法                                     |
| ----------------- | ---------------------------------------- |
| String 字符串     | 使用 String 字面量                       |
| Aggregation 聚合  | 对 CQL 查询结果执行一些聚合操作          |
| Relationship 关系 | 获取关系的细节，如 startnode，endnode 等 |

**Neo4j 数据类型**

| 数据类型 | 用法               |
| -------- | ------------------ |
| boolean  | 用于表示布尔值     |
| byte     | 用于表示8位整数    |
| short    | 用于表示16位整数   |
| int      | 用于表示32位整数   |
| long     | 用于表示64位整数   |
| float    | 用于表示32位浮点数 |
| double   | 用于表示64位浮点数 |
| char     | 用于表示16位字符   |
| String   | 用于表示字符串     |

### Neo4j CQL 命令

**命令介绍的前置说明**

语法说明：

| 语法元素         | 描述                         |
| ---------------- | ---------------------------- |
| <node-name>      | 节点的名称                   |
| <label-name>     | 节点的标签名称               |
| <fnode-name>              | 关系的“From Node”的名称     |
| <fnode-label-name>        | 关系的“From Node”的标签名称 |
| <tnode-name>              | 关系的“To Node”的名称       |
| <tnode-label-name>        | 关系的“To Node”的标签名称   |
| <Property-name>  | 属性是键值对，定义属性的名称 |
| <Property-value> | 属性是键值对，定义属性的值   |
| <relationship-name>       | 关系的名称                  |
| <relationship-label-name> | 关系的标签名称              |
| <boolean-operator>    | 布尔运算符               |
| <comparison-operator> | 比较运算符               |
| <value>               | 字面值，如数字、字符串等 |

布尔运算符：

| 布尔运算符 | 描述     |
| ---------- | -------- |
| AND        | 与操作   |
| OR         | 或操作   |
| NOT        | 非操作   |
| XOR        | 异或操作 |

比较运算符：

| 比较运算符 | 描述             |
| ---------- | ---------------- |
| =          | “等于”运算符     |
| <>         | “不等于”运算符   |
| <          | “小于”运算符     |
| >          | “大于”运算符     |
| <=         | “小于等于”运算符 |
| >=         | “大于等于”运算符 |

**Neo4j CREATE 命令**

命令语法：

```CQL
CREATE (<node-name>:<label-name>)

CREATE (
    <node-name>:<label-name>
    { 	
        <Property1-name>:<Property1-Value>
        ......
        <Propertyn-name>:<Propertyn-Value>
    }
)
```

**Neo4j MATCH 命令**

命令语法：

```CQL
MATCH 
(
    <node-name>:<label-name>
)
```

**Neo4j RETURN 命令**

命令语法：

```CQL
RETURN 
    <node-name>.<property1-name>,
    ......
    <node-name>.<propertyn-name>
```

**Neo4j MATCH+RETURN**

命令语法：

```CQL
MATCH Command
RETURN Command
```

语法说明：

| 语法元素 | 描述                  |
| -------- | --------------------- |
| MATCH    | Neo4j CQL MATCH 命令  |
| RETURN   | Neo4j CQL RETURN 命令 |

**Neo4j CQL 关系基础**

Neo4j 图数据库遵循属性图模型来存储和管理其数据。

在属性图数据模型中，关系应该是定向的。

基于方向性，Neo4j 关系分为两类：单向关系和双向关系。

命令语法（为已有节点创建关系）：

```CQL
MATCH (<fnode-label-name>:<fnode-name>),(<tnode-label-name>:<tnode-name>)
CREATE 
	(<fnode-label-name>)-
	[<relationship-label-name>:<relationship-name> {<define-properties-list>}]->
	(<tnode-label-name>)
RETURN <relationship-label-name>
```

命令语法（为新节点创建关系）：

```CQL
CREATE 
    (<fnode-label-name>:<node1-name>)-
    [<relationship-label-name>:<relationship-name> {<define-properties-list>}]->
    (<tnode-label-name>:<node1-name>)
RETURN <relationship-label-name>
```

命令语法（检索关系节点信息）：

```CQL
MATCH 
    (<fnode-label-name>)-
    [<relationship-label-name>:<relationship-name>]->
    (<tnode-label-name>)
RETURN <relationship-label-name>
```

**Neo4j CQL 创建标签**

命令语法（节点多标签）：

```CQL
CREATE (<node-name>:<label-name1>:<label-name2>:......:<label-namen>)
```

命令语法（关系单标签）：

```CQL
CREATE 
    (<node1-name>:<label1-name>)-
    [(<relationship-name>:<relationship-label-name>)]->
    (<node2-name>:<label2-name>)
```

**Neo4j CQL WHERE 子句**

命令语法：

```CQL
WHERE <condition>
WHERE <condition> <boolean-operator> <condition>

# <condition> 语法
<property-name> <comparison-operator> <value>
```

**Neo4j CQL DELETE 命令**

命令语法：

```CQL
DELETE <node-name-list>
DELETE <relationship-name-list>
```

**Neo4j CQL REMOVE 命令**

REMOVE 命令用于：删除节点或关系的标签；删除节点或关系的属性。

命令语法：

```CQL
REMOVE <property-name-list>

# <property-name-list> 语法
<node-name>.<property1-name>, 
...... 
<node-name>.<propertyn-name> 
```

```CQL
REMOVE <label-name-list>

# <label-name-list> 语法
<node-name>:<label1-name>, 
...... 
<node-name>:<labeln-name>
```

**Neo4j CQL SET 子句**

命令语法：

```CQL
SET  <property-name-value-list>

# <property-name-value-list> 语法
<property-name1> = <value>, 
......
<property-namen> = <value>
```

**Neo4j CQL 排序**

```CQL
ORDER BY <property-name-list>  [DESC]
```

**Neo4j CQL UNION 子句**

命令语法：

```CQL
<MATCH Command1>
UNION [ALL]
<MATCH Command2>
```

> 注意：结果列类型和来自两组结果的名称必须匹配，这意味着列名称应该相同，列的数据类型应该相同。UNION 与 UNION ALL 的区别在于前者不返回重复的行而后者会返回重复的行。

为避免虽然有相同的属性名但有不同的节点名称前缀的错误，Neo4j 提供了 “AS” 子句：

```CQL
RETURN <property-name> AS <new-property-name>
```

举个栗子：

```CQL
MATCH (cc:CreditCard)
RETURN cc.id AS id,cc.number AS number,cc.name AS name,
       cc.valid_from AS valid_from,cc.valid_to AS valid_to
UNION
MATCH (dc:DebitCard)
RETURN dc.id AS id,dc.number AS number,dc.name AS name,
       dc.valid_from AS valid_from,dc.valid_to AS valid_to
```

**Neo4j CQL LIMIT/SKIP 子句**

命令语法：

```CQL
LIMIT <number>
SKIP <number>
```

**Neo4j CQL 合并**

命令语法：

```CQL
# MERGE = CREATE + MATCH
# 若图中存在匹配则返回结果，否则创建
MERGE (<node-name>:<label-name>
{
   <property1-name>:<property1-value>
   .....
   <propertyn-name>:<propertyn-value>
})
```

**Neo4j CQL NULL 值**

Neo4j CQL 将空值视为对节点或关系的属性的缺失值或未定义值。

可以配合 WHERE 子句过滤包含 NULL 值的行：

```CQL
WHERE <property-name> IS NOT NULL
```

**Neo4j CQL IN 操作符**

命令语法：

```CQL
IN [<Collection-of-values>]
```

可以配合 WHERE 子句过滤不满足条件的行：

```CQL
# 当 <property-value> 不在集合 [<Collection-of-values>] 中时，该行将被过滤掉
WHERE <property-name> IN [<Collection-of-values>]
```

### Neo4j CQL 函数

**Neo4j CQL 字符串函数**

字符串函数列表：

| 函数      | 描述                     |
| --------- | ------------------------ |
| UPPER     | 将所有字母更改为大写字母 |
| LOWER     | 将所有字母改为小写字母   |
| SUBSTRING | 获取给定String的子字符串 |
| REPLACE   | 替换一个字符串的子字符串 |

函数语法：

```CQL
UPPER(<input-string>)
LOWER(<input-string>)
SUBSTRING(<input-string>,<startIndex>,<endIndex>)
REPLACE(<input-string>,<oldString>,<newString>)
```

**Neo4j CQL 聚合函数**

聚合函数列表：

| 函数  | 描述                                  |
| ----- | ------------------------------------- |
| COUNT | 返回由 MATCH 命令返回的行数           |
| MAX   | 返回由 MATCH 命令返回的一组行的最大值 |
| MIN   | 返回由 MATCH 命令返回的一组行的最小值 |
| SUM   | 返回由 MATCH 命令返回的所有行的求和值 |
| AVG   | 返回由 MATCH 命令返回的所有行的平均值 |

函数语法：

```CQL
COUNT(<value>)
MAX(<property-name>)
MIN(<property-name>)
SUM(<property-name>)
AVG(<property-name>)
```

**Neo4j CQL 关系函数**

关系函数列表：

| 功能      | 描述                              |
| --------- | --------------------------------- |
| STARTNODE | 获取关系的开始节点                |
| ENDNODE   | 获取关系的结束节点                |
| ID        | 获取关系的 ID                     |
| TYPE      | 获取字符串表示中的一个关系的 TYPE |

函数语法：

```CQL
STARTNODE(<relationship-label-name>)
ENDNODE(<relationship-label-name>)
ID(<relationship-label-name>)
TYPE(<relationship-label-name>)
```

## Neo4j Admin

### Neo4j 数据库备份和恢复

**数据备份**

```shell
$ neo4j stop   # 不支持热备份，需要先停机
$ neo4j-admin dump [--database=<name>] --to=<destination-path>
$ neo4j start
```

**数据恢复**

```shell
$ neo4j stop   # 同上
$ neo4j-admin load --from=<archive-path> [--database=<name>] [--force[=<true|false>]]
$ neo4j start
```

### Neo4j CQL 索引

Neo4j SQL支持节点或关系属性上的索引，以提高应用程序的性能。

可以为具有相同标签名称的所有节点的属性创建索引。

可以在 MATCH 或 WHERE 或 IN 运算符上使用这些索引列来改进 CQL Command 的执行。

命令语法：

```CQL
# 创建索引
CREATE INDEX ON :<label-name> (<property-name>)

# 显式使用索引进行查询
USING INDEX <node-name>:<label-name> (<property-name>)

# 丢弃索引
DROP INDEX ON :<label-name> (<property-name>)
```

举个栗子：

```CQL
CREATE INDEX ON :Customer (name)

MATCH (cc:CreditCard)
USING INDEX cc:CreditCard (number)
WHERE cc.number=111111
RETURN cc

DROP INDEX ON :Customer (name)
```

查看已创建索引/约束：

```
$ :schema
——————————————————————————————————————————————————————————————————
Indexes
   ON :CreditCard(number) ONLINE  (for uniqueness constraint)

Constraints
   ON ( creditcard:CreditCard ) ASSERT creditcard.number IS UNIQUE
```

### Neo4j CQL UNIQUE 约束

命令语法：

```CQL
# 创建 UNIQUE 约束
CREATE CONSTRAINT ON (<label_name>)
ASSERT <property_name> IS UNIQUE

# 丢弃 UNIQUE 约束
DROP CONSTRAINT ON (<label_name>)
ASSERT <property_name> IS UNIQUE
```

举个栗子：

```CQL
# 创建约束
CREATE CONSTRAINT ON (cc:CreditCard)
ASSERT cc.number IS UNIQUE

# 丢弃约束
DROP CONSTRAINT ON (cc:CreditCard)
ASSERT cc.number IS UNIQUE
```

> 注意：在某属性上创建 UNIQUE 约束后，一旦试图插入在该属性上重复的记录就会报错。

## Neo4j & Java

Neo4j 提供 JAVA API 以编程方式执行所有数据库操作。

支持两种类型的 API：

- Neo4j 原生的 Java API
- Neo4j Cypher Java API

## Neo4j & Python

### py2neo 库的使用

