# W3C - RDF 查询语言 SPARQL

教程：[W3C RDF 查询语言 SPARQL](<https://www.w3.org/TR/rdf-sparql-query/>) 

[TOC]

## 简介

SPARQL 即 SPARQL Protocol and RDF Query Language 的递归缩写，专门用于访问和操作 RDF 数据，是语义网的核心技术之一。

SPARQL 由两个部分组成：协议和查询语言：

- 查询语言：正如 SQL 用于查询关系数据库中的数据，XQuery 用于查询 XML 数据，SPARQL 用于查询 RDF 数据。
- 协议：可以通过 HTTP 协议在客户端和 SPARQL 服务器（SPARQL endpoint）之间传输查询和结果，这也是和其他查询语言最大的区别。

**一些约定**

命名空间绑定：

| Prefix  | IRI                                           |
| ------- | --------------------------------------------- |
| `rdf:`  | `http://www.w3.org/1999/02/22-rdf-syntax-ns#` |
| `rdfs:` | `http://www.w3.org/2000/01/rdf-schema#`       |
| `xsd:`  | `http://www.w3.org/2001/XMLSchema#`           |
| `fn:`   | `http://www.w3.org/2005/xpath-functions#`     |

数据描述语言：[Turtle](<https://www.w3.org/TeamSubmission/turtle/>) - 一种具体的 RDF 文本语法

## 简单查询

SPARQL查询是基于图匹配的思想。将查询与 RDF 图进行匹配，找到符合该匹配模式的所有子图，最后得到变量的值。简而言之，SPARQL 查询分为三个步骤：

1. 构建查询图模式，表现形式就是带有变量的RDF。
2. 匹配，匹配到符合指定图模式的子图。
3. 绑定，将结果绑定到查询图模式对应的变量上。

### 简单查询

一个示例：

```SPARQL
--> 数据
<http://example.org/book/book1> <http://purl.org/dc/elements/1.1/title> "SPARQL Tutorial" .
```

```SPARQL
--> 查询
SELECT ?title
WHERE
{
    <http://example.org/book/book1> <http://purl.org/dc/elements/1.1/title> ?title .
}
```

```
--> 结果
| title             |
| ----------------- |
| "SPARQL Tutorial" |
```

查询由两部分组成：`SELECT` 子句标识要在查询结果显示的变量，`WHERE` 子句标识要与数据图匹配的基本图模式。

### 多重匹配

```SPARQL
--> 数据
@prefix foaf:  <http://xmlns.com/foaf/0.1/> .

_:a  foaf:name   "Johnny Lee Outlaw" .
_:a  foaf:mbox   <mailto:jlow@example.com> .
_:b  foaf:name   "Peter Goodguy" .
_:b  foaf:mbox   <mailto:peter@example.org> .
_:c  foaf:mbox   <mailto:carol@example.org> .
```

```SPARQL
--> 查询
PREFIX foaf:   <http://xmlns.com/foaf/0.1/>
SELECT ?name ?mbox
WHERE
{
    ?x foaf:name ?name .
    ?x foaf:mbox ?mbox .
}
```

```
--> 结果
| name	              | mbox                       |
| ------------------- + -------------------------- |
| "Johnny Lee Outlaw" | <mailto:jlow@example.com>  |
| "Peter Goodguy"     | <mailto:peter@example.org> |
```

### 匹配字面量

```SPARQL
--> 数据
@prefix dt:   <http://example.org/datatype#> .
@prefix ns:   <http://example.org/ns#> .
@prefix :     <http://example.org/ns#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .

:x   ns:p     "cat"@en .
:y   ns:p     "42"^^xsd:integer .
:z   ns:p     "abc"^^dt:specialDatatype .
```

语言标签：

```SPARQL
SELECT ?v WHERE { ?v ?p "cat"@en }
```

数字类型：

```SPARQL
SELECT ?v WHERE { ?v ?p 42 }
```

任意数据类型：

```SPARQL
SELECT ?v WHERE { ?v ?p "abc"^^<http://example.org/datatype#specialDatatype> }
```

### 空白结点标签

```SPARQL
--> 数据
@prefix foaf:  <http://xmlns.com/foaf/0.1/> .

_:a  foaf:name   "Alice" .
_:b  foaf:name   "Bob" .
```

```SPARQL
--> 查询
PREFIX foaf:   <http://xmlns.com/foaf/0.1/>
SELECT ?x ?name
WHERE  { ?x foaf:name ?name }
```

```
--> 结果
| x	  | name    |
| --- + ------- |
| _:c | "Alice" |
| _:d | "Bob"   |
```

### 构建 RDF 图

SPARQL 有几种查询形式：`SELECT` 形式返回变量绑定；`CONSTRUCT` 形式返回 RDF 图，该图基于模板（可以基于匹配结果生成 RDF 三元组）构建。

```SPARQL
--> 数据
@prefix org:    <http://example.com/ns#> .

_:a  org:employeeName   "Alice" .
_:a  org:employeeId     12345 .

_:b  org:employeeName   "Bob" .
_:b  org:employeeId     67890 .
```

```SPARQL
--> 查询
PREFIX foaf:   <http://xmlns.com/foaf/0.1/>
PREFIX org:    <http://example.com/ns#>

CONSTRUCT { ?x foaf:name ?name }
WHERE { ?x org:employeeName ?name }
```

```SPARQL
--> 结果
@prefix org: <http://example.com/ns#> .
      
_:x foaf:name "Alice" .
_:y foaf:name "Bob" .
```

```xml
--> RDF/XML 序列化
<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#" 
         xmlns:foaf="http://xmlns.com/foaf/0.1/">
  <rdf:Description>
    <foaf:name>Alice</foaf:name>
  </rdf:Description>
  <rdf:Description>
    <foaf:name>Bob</foaf:name>
  </rdf:Description>
</rdf:RDF>
```

## RDF 条件约束/过滤器

```SPARQL
--> 数据
@prefix dc:   <http://purl.org/dc/elements/1.1/> .
@prefix :     <http://example.org/book/> .
@prefix ns:   <http://example.org/ns#> .

:book1  dc:title  "SPARQL Tutorial" .
:book1  ns:price  42 .
:book2  dc:title  "The Semantic Web" .
:book2  ns:price  23 .
```

### 约束字符串值

```SPARQL
--> 查询
PREFIX  dc:  <http://purl.org/dc/elements/1.1/>
SELECT  ?title
WHERE
{
    ?x dc:title ?title
    FILTER regex(?title, "^SPARQL") 
}
```

```
--> 结果
| title             |
| ----------------- |
| "SPARQL Tutorial" |
```

正则表达式匹配可以使用 `i` 标志大小写不敏感：`FILTER regex(?title, "web", "i")`。

正则表达式语言基于 [XML Schema Regular Expressions](<http://www.w3.org/TR/xmlschema-2/#regexs>)。

### 约束数字值

```SPARQL
--> 查询
PREFIX  dc:  <http://purl.org/dc/elements/1.1/>
PREFIX  ns:  <http://example.org/ns#>
SELECT  ?title ?price
WHERE
{
    ?x ns:price ?price .
    FILTER (?price < 30.5)
    ?x dc:title ?title .
}
```

```
--> 结果
| title              | price |
| ------------------ + ----- |
| "The Semantic Web" | 23    |
```

### 其他约束

除了以上约束，还有 `xsd:string`、`xsd:boolean`、`xsd:dateTime` 等诸多类型约束。

## SPARQL 语法

[详见 附录 A. SPARQL 语法](<https://www.w3.org/TR/rdf-sparql-query/#grammar>) 

### RDF 协议语法

**IRIs 语法**

一些示例（描述同一 IRI 的 3 种方式）：

```SPARQL
<http://example.org/book/book1>

BASE <http://example.org/book/>
<book1>

PREFIX book: <http://example.org/book/>
book:book1
```

**字面量语法**

字面量一般语法是一个字符串，带有可选的语言标签（由 `@` 引入）或可选的数据类型 IRI 或前缀名称（由 `^^` 引入）。

一些示例：

```SPARQL
"chat"
'chat'@fr
"xyz"^^<http://example.org/ns/userDatatype>
"abc"^^appNS:appDataType
'''The librarian said, "Perhaps you would enjoy 'War and Peace'."'''
1, which is the same as "1"^^xsd:integer
1.3, which is the same as "1.3"^^xsd:decimal
1.300, which is the same as "1.300"^^xsd:decimal
1.0e6, which is the same as "1.0e6"^^xsd:double
true, which is the same as "true"^^xsd:boolean
false, which is the same as "false"^^xsd:boolean
```

**查询变量语法**

查询变量具有全局性，以 `?` 或 `$` 为前缀，`?var` 与 `$var` 标识同一变量。

| `Var`     | ::=  | `VAR1 | VAR2`                                                |
| --------- | ---- | ------------------------------------------------------------ |
| `VAR1`    | ::=  | `'?' VARNAME`                                                |
| `VAR2`    | ::=  | `'$' VARNAME`                                                |
| `VARNAME` | ::=  | `( PN_CHARS_U | [0-9] ) ( PN_CHARS_U | [0-9] | #x00B7 | [#x0300-#x036F] | [#x203F-#x2040] )*` |

**空白结点语法**

空白结点充当图模式的非区分变量，而不是对查询数据中的特定空白结点的引用。相同的空白结点标签不能在同一查询的两个单独的基本图模式中被使用。

空白结点由标签形式表示，如 `_:abc` 或缩写 `[]`。

含有空白结点的三元组：

```SPARQL
--> 两种格式
[ :p "v" ] .
[] :p "v" .
--> 指定空白结点唯一标签
_:b57 :p "v" .
```

可以作为其他三元组的组成：

```SPARQL
[ :p "v" ] :q "w" .
--> 等价于两个三元组
:x :q _:b32 .
_:b32 :p "v" .
```

空白结点缩写格式可与其他缩写（共享主语/宾语）组合：

```SPARQL
[ foaf:name  ?name ;
  foaf:mbox  <mailto:alice@example.org> ]
--> 等价于
_:b18  foaf:name  ?name .
_:b18  foaf:mbox  <mailto:alice@example.org> .
```

### 三元组模式语法

| `TriplesSameSubject`   | ::=  | `VarOrTerm PropertyListNotEmpty |TriplesNode PropertyList` |
| ---------------------- | ---- | ---------------------------------------------------------- |
| `PropertyListNotEmpty` | ::=  | `Verb ObjectList ( ';' ( Verb ObjectList )? )*`            |
| `PropertyList`         | ::=  | `PropertyListNotEmpty?`                                    |
| `ObjectList`           | ::=  | `Object ( ',' Object )*`                                   |
| `Verb`                 | ::=  | `VarOrIRIref | 'a'`                                        |

**谓语 - 宾语 列表**

共享主语的三元组，可以只写入一次主语，然后通过 `;` 符号分隔多个三元组。

```SPARQL
?x  foaf:name  ?name ;
    foaf:mbox  ?mbox .
--> 等价于
?x  foaf:name  ?name .
?x  foaf:mbox  ?mbox .
```

**宾语 列表**

同时共享主语和谓语的三元组，可以只写入一次主谓语，然后通过 `,` 符号分隔多个三元组。

```SPARQL
?x foaf:nick  "Alice" , "Alice_" .
--> 等价于
?x  foaf:nick  "Alice" .
?x  foaf:nick  "Alice_" .
```

以上可以进行组合：

```SPARQL
?x  foaf:name ?name ; foaf:nick  "Alice" , "Alice_" .
--> 等价于
?x  foaf:name  ?name .
?x  foaf:nick  "Alice" .
?x  foaf:nick  "Alice_" .
```

**RDF 集合**

| `Collection` | ::=  | `'(' GraphNode+ ')'` |
| ------------ | ---- | -------------------- |
| `NIL`        | ::=  | `'(' WS* ')'`        |

使用语法 `(element1 element2 ...)` 描述 RDF 集合。`()` 格式是 IRI <http://www.w3.org/1999/02/22-rdf-syntax-ns#nil> 的缩写。

```SPARQL
(1 ?x 3 4) :p "w" .
--> 等价于
_:b0  rdf:first  1 ;
      rdf:rest   _:b1 .
_:b1  rdf:first  ?x ;
      rdf:rest   _:b2 .
_:b2  rdf:first  3 ;
      rdf:rest   _:b3 .
_:b3  rdf:first  4 ;
      rdf:rest   rdf:nil .
_:b0  :p         "w" . 
```

RDF 集合可以进行嵌套：

```SPARQL
(1 [:p :q] ( 2 ) ) .
--> 等价于
_:b0  rdf:first  1 ;
      rdf:rest   _:b1 .
_:b1  rdf:first  _:b2 .
_:b2  :p         :q .
_:b1  rdf:rest   _:b3 .
_:b3  rdf:first  _:b4 .
_:b4  rdf:first  2 ;
      rdf:rest   rdf:nil .
_:b3  rdf:rest   rdf:nil .
```

**rdf:type**

关键字 `a` 可以用作谓语，是 IRI <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> 的替代。

```SPARQL
?x  a  :Class1 .
[ a :appClass ] :p "v" .
--> 等价于
?x    rdf:type  :Class1 .
_:b0  rdf:type  :appClass .
_:b0  :p        "v" .
```

## 图模式

SPARQL 基于图形模式匹配。通过以各种方式组合较小的模式，可以形成更复杂的图形模式：

- 基本图形模式，其中一组三重模式必须匹配
- 组图模式，其中一组图模式必须全部匹配
- 可选的图形模式，其他模式可以扩展解决方案
- 替代图模式，其中尝试了两种或更多种可能的模式
- 命名图模式，其中模式与命名图匹配

查询中最外层的图模式称为查询模式。

| `WhereClause`       | ::=  | `'WHERE'? GroupGraphPattern`                                 |
| ------------------- | ---- | ------------------------------------------------------------ |
| `GroupGraphPattern` | ::=  | `'{' TriplesBlock? ( ( GraphPatternNotTriples | Filter ) '.'? TriplesBlock? )* '}'` |

### 基本图模式

基本图形模式是三重模式的集合。SPARQL 图模式匹配是根据匹配基本图模式的结果进行定义的。

**空白结点标签**

当使用 `_:abc` 格式时，空白结点的标签范围限定为基本图形模式。在任何查询中，标签只能用于单个基本图形模式。

**扩展基本图匹配**

SPARQL 定义为使用简单的蕴涵匹配 RDF 图。在给定某些条件的情况下，SPARQL 可以扩展到其他形式的蕴涵。

### 组图模式

| `GroupGraphPattern`      | ::=  | `'{' TriplesBlock? ( ( GraphPatternNotTriples | Filter ) '.'? TriplesBlock? )* '}'` |
| ------------------------ | ---- | ------------------------------------------------------------ |
| `TriplesBlock`           | ::=  | `TriplesSameSubject ( '.' TriplesBlock? )?`                  |
| `GraphPatternNotTriples` | ::=  | `OptionalGraphPattern | GroupOrUnionGraphPattern | GraphGraphPattern` |

在 SPARQL 查询语句中，组图模式使用 `{}` 分隔，如下（两种查询将获得同样结果）：

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT ?name ?mbox
WHERE 
{
    ?x foaf:name ?name .
    ?x foaf:mbox ?mbox .
}
```

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT ?name ?mbox
WHERE 
{
    { ?x foaf:name ?name . }
    { ?x foaf:mbox ?mbox . }
}
```

**空的组图模式**

空的组图模式 `{}`。通过不绑定任何变量，可以匹配任意图（包括空图）。

```SPARQL
SELECT ?x
WHERE {}
```

**过滤器**

由关键字 `FILTER` 表示的约束是对解决方案的约束。一个示例：

```SPARQL
{
    ?x foaf:name ?name .
    ?x foaf:mbox ?mbox .
    FILTER regex(?name, "Smith")
}
```

**组图模式示例**

```SPARQL
{
    ?x foaf:name ?name .
    ?x foaf:mbox ?mbox .
}
--> 过滤器不会将以上基本图模式分隔为两个基本图模式
{
    ?x foaf:name ?name .
    FILTER regex(?name, "Smith")
    ?x foaf:mbox ?mbox .
}
--> 空组图模式会将以上基本图模式分隔为两个基本图模式
{
    ?x foaf:name ?name .
    {}
    ?x foaf:mbox ?mbox .
}
```

## 包含可选值

| `OptionalGraphPattern` | ::=  | `'OPTIONAL' GroupGraphPattern`                               |
| ---------------------- | ---- | ------------------------------------------------------------ |
| `GroupGraphPattern`    | ::=  | `'{' TriplesBlock? ( ( GraphPatternNotTriples | Filter ) '.'? TriplesBlock? )* '}'` |

可选匹配提供此功能：如果可选部分不匹配，则不会创建任何绑定，但也不会消除解决方案。

### 可选模式匹配

通过 `OPTIONAL` 关键字指定图模式的可选部分：

```SPARQL
pattern OPTIONAL { pattern }
{ OPTIONAL { pattern } }
{ { } OPTIONAL { pattern } }
```

`OPTIONAL` 关键字为左结合，因此以下语句等价：

```SPARQL
pattern OPTIONAL { pattern } OPTIONAL { pattern }
{ pattern OPTIONAL { pattern } } OPTIONAL { pattern }
```

一个示例：

```SPARQL
--> 数据
@prefix foaf:       <http://xmlns.com/foaf/0.1/> .
@prefix rdf:        <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

_:a  rdf:type        foaf:Person .
_:a  foaf:name       "Alice" .
_:a  foaf:mbox       <mailto:alice@example.com> .
_:a  foaf:mbox       <mailto:alice@work.example> .

_:b  rdf:type        foaf:Person .
_:b  foaf:name       "Bob" .
```

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
SELECT ?name ?mbox
WHERE 
{
    ?x foaf:name  ?name .
    OPTIONAL { ?x  foaf:mbox  ?mbox }
}
```

```
--> 结果
| name    | mbox                        |
| ------- + --------------------------- |
| "Alice" | <mailto:alice@example.com>  |
| "Alice" | <mailto:alice@work.example> |
| "Bob"	  |                             |
```

备注：以上“Bob”对应的查询结果并没有“mbox”值。

### 可选模式匹配的约束

约束可以在可选图模式中给出。一个示例：

```SPARQL
--> 数据
@prefix dc:   <http://purl.org/dc/elements/1.1/> .
@prefix :     <http://example.org/book/> .
@prefix ns:   <http://example.org/ns#> .

:book1  dc:title  "SPARQL Tutorial" .
:book1  ns:price  42 .
:book2  dc:title  "The Semantic Web" .
:book2  ns:price  23 .
```

```SPARQL
--> 查询
PREFIX  dc:  <http://purl.org/dc/elements/1.1/>
PREFIX  ns:  <http://example.org/ns#>
SELECT  ?title ?price
WHERE 
{
    ?x dc:title ?title .
    OPTIONAL { ?x ns:price ?price . FILTER (?price < 30) }
}
```

```
--> 结果
| title              | price |
| ------------------ + ----- |
| "SPARQL Tutorial"  |       |
| "The Semantic Web" | 23    |
```

备注：以上“SPARQL Tutorial”对应的查询结果并没有“price”值（“price”值不满足 FILTER 约束）。

### 多可选图模式

图模式是递归定义的，可以具有零个或多个可选图模式，并且查询图模式的任何部分都可以具有可选部分。

```SPARQL
--> 数据
@prefix foaf:       <http://xmlns.com/foaf/0.1/> .

_:a  foaf:name       "Alice" .
_:a  foaf:homepage   <http://work.example.org/alice/> .

_:b  foaf:name       "Bob" .
_:b  foaf:mbox       <mailto:bob@work.example> .
```

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
SELECT ?name ?mbox ?hpage
WHERE 
{
    ?x foaf:name  ?name .
    OPTIONAL { ?x foaf:mbox ?mbox } .
    OPTIONAL { ?x foaf:homepage ?hpage }
}
```

```
--> 结果
| name    | mbox                      | hpage                            |
| ------- + ------------------------- + -------------------------------- |
| "Alice" |                           | <http://work.example.org/alice/> |
| "Bob"   | <mailto:bob@work.example> |                                  |
```

## 备选匹配

| `GroupOrUnionGraphPattern` | ::=  | `GroupGraphPattern ( 'UNION' GroupGraphPattern )*`           |
| -------------------------- | ---- | ------------------------------------------------------------ |
| `GroupGraphPattern`        | ::=  | `'{' TriplesBlock? ( ( GraphPatternNotTriples | Filter ) '.'? TriplesBlock? )* '}'` |

SPARQL 提供了一种图模式的组合方法，以便几个备选图模式中的任意一个可以匹配的情况。如果有多个备选匹配，则找到所有可能的模式。使用 `UNION` 关键字指定备选模式。

```SPARQL
--> 数据
@prefix dc10:  <http://purl.org/dc/elements/1.0/> .
@prefix dc11:  <http://purl.org/dc/elements/1.1/> .

_:a  dc10:title     "SPARQL Query Language Tutorial" .
_:a  dc10:creator   "Alice" .

_:b  dc11:title     "SPARQL Protocol Tutorial" .
_:b  dc11:creator   "Bob" .

_:c  dc10:title     "SPARQL" .
_:c  dc11:title     "SPARQL (updated)" .
```

```SPARQL
--> 查询
PREFIX dc10:  <http://purl.org/dc/elements/1.0/>
PREFIX dc11:  <http://purl.org/dc/elements/1.1/>

SELECT ?title
WHERE  { { ?book dc10:title  ?title } UNION { ?book dc11:title  ?title } }
```

```
--> 结果
| title                            |
| -------------------------------- |
| "SPARQL Protocol Tutorial"       |
| "SPARQL"                         |
| "SPARQL (updated)"               |
| "SPARQL Query Language Tutorial" |
```

为了更确切的信息，上例可以使用两个变量：

```SPARQL
--> 查询
PREFIX dc10:  <http://purl.org/dc/elements/1.0/>
PREFIX dc11:  <http://purl.org/dc/elements/1.1/>

SELECT ?xtitle ?ytitle
WHERE  { { ?book dc10:title ?xtitle } UNION { ?book dc11:title  ?ytitle } }
```

```
--> 结果
| xtitle                           | ytitle                           |
| -------------------------------- + -------------------------------- |
|                                  | "SPARQL Protocol Tutorial"       |
|                                  | "SPARQL"                         |
| "SPARQL (updated)"               |                                  |
| "SPARQL Query Language Tutorial" |                                  |
```

每个备选匹配图都可以是包含多个三元组的复杂模式图：

```SPARQL
--> 查询
PREFIX dc10:  <http://purl.org/dc/elements/1.0/>
PREFIX dc11:  <http://purl.org/dc/elements/1.1/>

SELECT ?title ?author
WHERE
{
    { ?book dc10:title ?title .  ?book dc10:creator ?author }
    UNION
    { ?book dc11:title ?title .  ?book dc11:creator ?author }
}
```

```
--> 结果
| author  | title                            |
| ------- + -------------------------------- |
| "Alice" | "SPARQL Protocol Tutorial"       |
| "Bob"   | "SPARQL Query Language Tutorial" |
```

## RDF 数据集

RDF 数据模型将信息表示为包含许多主谓宾三元组的图，许多 RDF 数据存储包含多个 RDF 图并记录图的相关信息，允许进行涉及多个图的信息查询。

RDF 数据集包含一个默认图（没有命名）以及零个或多个命名图，每个命名图由 IRI 标识。

### RDF 数据集示例

示例一：

```SPARQL
# Default graph
@prefix dc: <http://purl.org/dc/elements/1.1/> .

<http://example.org/bob>    dc:publisher  "Bob" .
<http://example.org/alice>  dc:publisher  "Alice" .

# Named graph: http://example.org/bob
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Bob" .
_:a foaf:mbox <mailto:bob@oldcorp.example.org> .

# Named graph: http://example.org/alice
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example.org> .
```

示例二：

```SPARQL
# Default graph
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:x foaf:name "Bob" .
_:x foaf:mbox <mailto:bob@oldcorp.example.org> .

_:y foaf:name "Alice" .
_:y foaf:mbox <mailto:alice@work.example.org> .

# Named graph: http://example.org/bob
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Bob" .
_:a foaf:mbox <mailto:bob@oldcorp.example.org> .

# Named graph: http://example.org/alice
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example> .
```

### 指定 RDF 数据集

| `DatasetClause`      | ::=  | `'FROM' ( DefaultGraphClause | NamedGraphClause )` |
| -------------------- | ---- | -------------------------------------------------- |
| `DefaultGraphClause` | ::=  | `SourceSelector`                                   |
| `NamedGraphClause`   | ::=  | `'NAMED' SourceSelector`                           |
| `SourceSelector`     | ::=  | `IRIref`                                           |

SPARQL 查询可以使用 `FROM` 子句和 `FROM NAMED` 子句来指定要用于匹配的数据集。

RDF 数据集也可以在 SPARQL 协议请求中指定，此时，协议描述会覆盖查询描述。

**指定默认图**

默认图由 `FROM` 关键字指定。

```SPARQL
--> 数据，一个默认图
# Default graph (stored at http://example.org/foaf/aliceFoaf)
@prefix  foaf:  <http://xmlns.com/foaf/0.1/> .

_:a  foaf:name     "Alice" .
_:a  foaf:mbox     <mailto:alice@work.example> .
```

```SPARQL
--> 查询，如果包含多个FROM子句提供IRI来指定默认图，则默认图基于给定IRIs指定图的RDF合并
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
SELECT  ?name
FROM    <http://example.org/foaf/aliceFoaf>
WHERE   { ?x foaf:name ?name }
```

**指定命名图**

命名图由 `FROM NAMED` 关键字指定。

```SPARQL
--> 数据，两个命名图
# Graph: http://example.org/bob
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Bob" .
_:a foaf:mbox <mailto:bob@oldcorp.example.org> .

# Graph: http://example.org/alice
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example> .
```

```
--> 查询，每个IRI提供一个命名图，重复相同的IRI仅提供一个命名图
...
FROM NAMED <http://example.org/alice>
FROM NAMED <http://example.org/bob>
...
```

在 RDF 数据集中，IRI 与图之间的关系是间接的，IRI 标识一个资源，资源由图来表示（更确切地说，资源由图的序列化文档来表示）。

**组合 `FROM` 与 `FROM NAMED`**

`FROM` 子句和 `FROM NAMED` 子句可以在同一个查询中使用。一个示例：

```SPARQL
--> 数据
# Default graph (stored at http://example.org/dft.ttl)
@prefix dc: <http://purl.org/dc/elements/1.1/> .

<http://example.org/bob>    dc:publisher  "Bob Hacker" .
<http://example.org/alice>  dc:publisher  "Alice Hacker" .

# Named graph: http://example.org/bob
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Bob" .
_:a foaf:mbox <mailto:bob@oldcorp.example.org> .

# Named graph: http://example.org/alice
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example.org> .
```

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX dc: <http://purl.org/dc/elements/1.1/>

SELECT ?who ?g ?mbox
FROM <http://example.org/dft.ttl>
FROM NAMED <http://example.org/alice>
FROM NAMED <http://example.org/bob>
WHERE
{
   ?g dc:publisher ?who .
   GRAPH ?g { ?x foaf:mbox ?mbox }
}
```

以上查询的 RDF 数据集包含一个默认图和两个命名图。

### 查询数据集

| `GraphGraphPattern` | ::=  | `'GRAPH' VarOrIRIref GroupGraphPattern`                      |
| ------------------- | ---- | ------------------------------------------------------------ |
| `VarOrIRIref`       | ::=  | `Var | IRIref`                                               |
| `GroupGraphPattern` | ::=  | `'{' TriplesBlock? ( ( GraphPatternNotTriples | Filter ) '.'? TriplesBlock? )* '}'` |

查询多个图的集合时，`GRAPH` 关键字用来指定匹配的命名图，可以通过提供 IRI 选择一个图。

通过 `GRAPH` 关键字更改活动图。

```SPARQL
--> 数据
# Named graph: http://example.org/foaf/aliceFoaf
@prefix  foaf:     <http://xmlns.com/foaf/0.1/> .
@prefix  rdf:      <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix  rdfs:     <http://www.w3.org/2000/01/rdf-schema#> .

_:a  foaf:name     "Alice" .
_:a  foaf:mbox     <mailto:alice@work.example> .
_:a  foaf:knows    _:b .

_:b  foaf:name     "Bob" .
_:b  foaf:mbox     <mailto:bob@work.example> .
_:b  foaf:nick     "Bobby" .
_:b  rdfs:seeAlso  <http://example.org/foaf/bobFoaf> .

<http://example.org/foaf/bobFoaf>
     rdf:type      foaf:PersonalProfileDocument .

# Named graph: http://example.org/foaf/bobFoaf
@prefix  foaf:     <http://xmlns.com/foaf/0.1/> .
@prefix  rdf:      <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix  rdfs:     <http://www.w3.org/2000/01/rdf-schema#> .

_:z  foaf:mbox     <mailto:bob@work.example> .
_:z  rdfs:seeAlso  <http://example.org/foaf/bobFoaf> .
_:z  foaf:nick     "Robert" .

<http://example.org/foaf/bobFoaf>
     rdf:type      foaf:PersonalProfileDocument .
```

**访问图名称**

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?src ?bobNick
FROM NAMED <http://example.org/foaf/aliceFoaf>
FROM NAMED <http://example.org/foaf/bobFoaf>
WHERE
{
    GRAPH ?src {
        ?x foaf:mbox <mailto:bob@work.example> .
        ?x foaf:nick ?bobNick . }
}
```

```
--> 结果
| src                                 | bobNick  |
| ----------------------------------- + -------- |
| <http://example.org/foaf/aliceFoaf> | "Bobby"  |
| <http://example.org/foaf/bobFoaf>   | "Robert" |
```

**图 IRI 约束**

以下查询 `GRAPH` 通过提供一个 IRI 指定查询图对匹配范围进行约束。

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX data: <http://example.org/foaf/>

SELECT ?nick
FROM NAMED <http://example.org/foaf/aliceFoaf>
FROM NAMED <http://example.org/foaf/bobFoaf>
WHERE
  {
     GRAPH data:bobFoaf {
         ?x foaf:mbox <mailto:bob@work.example> .
         ?x foaf:nick ?nick }
  }
```

```
--> 结果
| nick     |
| -------- |
| "Robert" |
```

**约束可能的图 IRIs**

```SPARQL
--> 查询
PREFIX  data:  <http://example.org/foaf/>
PREFIX  foaf:  <http://xmlns.com/foaf/0.1/>
PREFIX  rdfs:  <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?mbox ?nick ?ppd
FROM NAMED <http://example.org/foaf/aliceFoaf>
FROM NAMED <http://example.org/foaf/bobFoaf>
WHERE
{
    GRAPH data:aliceFoaf
    {
        ?alice foaf:mbox <mailto:alice@work.example> ;
               foaf:knows ?whom .
        ?whom  foaf:mbox ?mbox ;
               rdfs:seeAlso ?ppd .
        ?ppd  a foaf:PersonalProfileDocument .
      } .
    GRAPH ?ppd
    {
        ?w foaf:mbox ?mbox ;
           foaf:nick ?nick
    }
}
```

```
--> 结果
| mbox                      | nick     | ppd                               |
| ------------------------- + -------- + --------------------------------- |
| <mailto:bob@work.example> | "Robert" | <http://example.org/foaf/bobFoaf> |
```

**命名图与默认图**

```SPARQL
--> 数据
# Default graph
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix g:  <tag:example.org,2005-06-06:> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

g:graph1 dc:publisher "Bob" .
g:graph1 dc:date "2004-12-06"^^xsd:date .

g:graph2 dc:publisher "Bob" .
g:graph2 dc:date "2005-01-10"^^xsd:date .

# Graph: locally allocated IRI: tag:example.org,2005-06-06:graph1
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example> .

_:b foaf:name "Bob" .
_:b foaf:mbox <mailto:bob@oldcorp.example.org> .

# Graph: locally allocated IRI: tag:example.org,2005-06-06:graph2
@prefix foaf: <http://xmlns.com/foaf/0.1/> .

_:a foaf:name "Alice" .
_:a foaf:mbox <mailto:alice@work.example> .

_:b foaf:name "Bob" .
_:b foaf:mbox <mailto:bob@newcorp.example.org> .
```

```SPARQL
--> 查询
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX dc:   <http://purl.org/dc/elements/1.1/>

SELECT ?name ?mbox ?date
WHERE
{
    ?g dc:publisher ?name ;
       dc:date ?date .
    GRAPH ?g
    { ?person foaf:name ?name ; foaf:mbox ?mbox }
}
```

```
--> 结果
| name  | mbox                             | date                   |
| ----- + -------------------------------- + ---------------------- |
| "Bob" | <mailto:bob@oldcorp.example.org> | "2004-12-06"^^xsd:date |
| "Bob" | <mailto:bob@newcorp.example.org> | "2005-01-10"^^xsd:date |
```

## 解决方案序列及修饰符

| `SelectQuery`        | ::=  | `'SELECT' ( 'DISTINCT' | 'REDUCED' )? ( Var+ | '*' ) DatasetClause* WhereClause SolutionModifier` |
| -------------------- | ---- | ------------------------------------------------------------ |
| `SolutionModifier`   | ::=  | `OrderClause? LimitOffsetClauses?`                           |
| `LimitOffsetClauses` | ::=  | `( LimitClause OffsetClause? | OffsetClause LimitClause? )`  |
| `OrderClause`        | ::=  | `'ORDER' 'BY' OrderCondition+`                               |

查询最初生成无序的解决方案集合，最终应用序列修饰符生成有序的解决方案序列

序列修饰符：

- Order：将解决方案依序排列
- Projection：选择某些变量
- Distinct：确保解决方案的唯一性
- Reduced：允许去除一些非唯一的解决方案
- Offset：控制在整个解决方案序列的起始位置
- Limit：限制解决方案数量

### ORDER BY

`ORDER BY` 子句建立解决方案序列的顺序。几个示例：

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>

SELECT ?name
WHERE { ?x foaf:name ?name }
ORDER BY ?name
```

```SPARQL
--> 查询
PREFIX     :    <http://example.org/ns#>
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
PREFIX xsd:     <http://www.w3.org/2001/XMLSchema#>

SELECT ?name
WHERE { ?x foaf:name ?name ; :empId ?emp }
ORDER BY DESC(?emp)
```

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>

SELECT ?name
WHERE { ?x foaf:name ?name ; :empId ?emp }
ORDER BY ?name DESC(?emp)
```

详情见 [ORDER BY](<https://www.w3.org/TR/rdf-sparql-query/#modOrderBy>)。

### Projection

```SPARQL
--> 数据
@prefix foaf:        <http://xmlns.com/foaf/0.1/> .

_:a  foaf:name       "Alice" .
_:a  foaf:mbox       <mailto:alice@work.example> .

_:b  foaf:name       "Bob" .
_:b  foaf:mbox       <mailto:bob@work.example> .
```

```SPARQL
--> 查询
PREFIX foaf:       <http://xmlns.com/foaf/0.1/>
SELECT ?name
WHERE { ?x foaf:name ?name }
```

```
--> 结果
| name    |
| ------- |
| "Bob"   |
| "Alice" |
```

### 重复解决方案

```SPARQL
--> 数据
@prefix  foaf:  <http://xmlns.com/foaf/0.1/> .

_:x    foaf:name   "Alice" .
_:x    foaf:mbox   <mailto:alice@example.com> .

_:y    foaf:name   "Alice" .
_:y    foaf:mbox   <mailto:asmith@example.com> .

_:z    foaf:name   "Alice" .
_:z    foaf:mbox   <mailto:alice.smith@example.com> .
```

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT ?name WHERE { ?x foaf:name ?name }
```

```SPARQL
--> 结果
| name    |
| ------- |
| "Alice" |
| "Alice" |
| "Alice" |
```

### DISTINCT

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT DISTINCT ?name WHERE { ?x foaf:name ?name }
```

```SPARQL
--> 结果
| name    |
| ------- |
| "Alice" |
```

### REDUCED

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT REDUCED ?name WHERE { ?x foaf:name ?name }
```

```SPARQL
--> 结果
| name    |
| ------- |
| "Alice" |
| "Alice" |
```

解决方案可能有一个、两个（此处显示）或三个“Alice”。

### OFFSET

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>

SELECT  ?name
WHERE   { ?x foaf:name ?name }
ORDER BY ?name
LIMIT   5
OFFSET  10
```

### LIMIT

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>

SELECT ?name
WHERE { ?x foaf:name ?name }
LIMIT 20
```

## 查询形式

SPARQL 有四种查询形式：

- SELECT：返回变量绑定的全集或子集
- CONSTRUCT：返回通过替换三元组模板中变量构造出的 RDF 图
- ASK：返回是否有匹配的布尔值
- DESCRIBE：返回描述匹配资源的 RDF 图

### SELECT

```SPARQL
--> 数据
@prefix  foaf:  <http://xmlns.com/foaf/0.1/> .

_:a    foaf:name   "Alice" .
_:a    foaf:knows  _:b .
_:a    foaf:knows  _:c .

_:b    foaf:name   "Bob" .

_:c    foaf:name   "Clare" .
_:c    foaf:nick   "CT" .
```

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
SELECT ?nameX ?nameY ?nickY
WHERE
{
    ?x foaf:knows ?y ;
       foaf:name ?nameX .
    ?y foaf:name ?nameY .
    OPTIONAL { ?y foaf:nick ?nickY }
}
```

```SPARQL
--> 结果
| nameX   | nameY   | nickY |
| ------- + ------- + ----- |
| "Alice" | "Bob"   |       |
| "Alice" | "Clare" | "CT"  |
```

结果集可以由本地 API 访问，也可以序列化为 XML 文档 或 RDF 图。一个 XML 格式示例：

```xml
<?xml version="1.0"?>
<sparql xmlns="http://www.w3.org/2005/sparql-results#">
  <head>
    <variable name="nameX"/>
    <variable name="nameY"/>
    <variable name="nickY"/>
  </head>
  <results>
    <result>
      <binding name="nameX">
        <literal>Alice</literal>
      </binding>
      <binding name="nameY">
        <literal>Bob</literal>
      </binding>
    </result>
    <result>
      <binding name="nameX">
        <literal>Alice</literal>
      </binding>
      <binding name="nameY">
        <literal>Clare</literal>
      </binding>
      <binding name="nickY">
        <literal>CT</literal>
      </binding>
    </result>
  </results>
</sparql>
```

### CONSTRUCT

```SPARQL
--> 数据
@prefix  foaf:  <http://xmlns.com/foaf/0.1/> .

_:a    foaf:name   "Alice" .
_:a    foaf:mbox   <mailto:alice@example.org> .
```

```SPARQL
--> 查询
PREFIX foaf:    <http://xmlns.com/foaf/0.1/>
PREFIX vcard:   <http://www.w3.org/2001/vcard-rdf/3.0#>
CONSTRUCT   { <http://example.org/person#Alice> vcard:FN ?name }
WHERE       { ?x foaf:name ?name }
```

