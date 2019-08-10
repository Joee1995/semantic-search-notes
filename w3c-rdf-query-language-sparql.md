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
WHERE  { ?x org:employeeName ?name }
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

> 备注：正则表达式匹配可以使用 `i` 标志大小写不敏感：`FILTER regex(?title, "web", "i")`。
>
> 备注：正则表达式语言基于 [XML Schema Regular Expressions](<http://www.w3.org/TR/xmlschema-2/#regexs>)。

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

```
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



## 包含可选值