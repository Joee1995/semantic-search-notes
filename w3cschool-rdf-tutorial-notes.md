# WCschool - RDF 教程笔记

教程：[W3Cschool - RDF 教程](<https://www.w3cschool.cn/rdf/?>) 

[TOC]

## RDF 简介

**RDF 示例**

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:si="//www.w3cschool.cn/rdf/">

<rdf:Description 
rdf:about="http://www.w3cschool.cn">
  <si:title>W3CSchool</si:title>
  <si:author>Jan Egil Refsnes</si:author>
</rdf:Description>

</rdf:RDF>
```

**什么是 RDF？**

- RDF 指资源描述框架（Resource Description Framework）
- RDF 是一个用于描述 Web 上的资源的框架
- RDF 提供了针对数据的模型以及语法，这样独立的团体们就可以交换和使用它
- RDF 被设计为可被计算机阅读和理解
- RDF 被设计的目的不是为了向人们显示出来
- RDF 使用 XML 编写
- RDF 是 W3C 语义网络活动的组成部分
- RDF 是一个 W3C 推荐标准

**RDF 被设计为可被计算机读取**

RDF 被设计为提供一种描述信息的通用方法，这样就可以被计算机应用程序读取并理解。

RDF 描述不是被设计用来在网络上显示的。

**RDF 使用 XML 编写**

RDF 文档使用 XML 编写。被 RDF 使用的 XML 语言被称为 RDF/XML。

通过使用 XML，RDF 信息可以轻易地在使用不同类型的操作系统和应用语言的计算机之间进行交换。

## RDF 规则

**RDF 资源、属性与属性值**

RDF 使用 Web 标识符来标识事物，并通过属性和属性值来描述资源。

对资源、属性和属性值的解释：

- **资源**是可拥有 URI 的任何事物，比如 "//www.w3cschool.cn/rdf"
- **属性**是拥有名称的资源，比如 "author" 或 "homepage"
- **属性值**是某个属性的值，比如 "David" 或 "http://www.w3cschool.cn" （属性值可以是另一个资源）

**RDF 陈述**

资源、属性和属性值的组合可形成一个*陈述*（被称为陈述的**主体**、**谓语**和**客体**）。

示例：

陈述："The author of //www.w3cschool.cn/rdf is David."

- 陈述的主体是：//www.w3cschool.cn/rdf
- 谓语是：author
- 客体是：David

陈述："The homepage of //www.w3cschool.cn/rdf is http://www.w3cschool.cn".

- 陈述的主体是：//www.w3cschool.cn/rdf
- 谓语是：homepage
- 客体是：http://www.w3cschool.cn

## RDF 实例

一个 CD 列表的几行：

| 标题             | 艺术家       | 国家 | 公司        | 价格  | 年份 |
| :--------------- | :----------- | :--- | :---------- | :---- | :--- |
| Empire Burlesque | Bob Dylan    | USA  | Columbia    | 10.90 | 1985 |
| Hide your heart  | Bonnie Tyler | UK   | CBS Records | 9.90  | 1988 |

对应的 RDF 文档：

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:cd="http://www.recshop.fake/cd#">

<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Empire Burlesque">
  <cd:artist>Bob Dylan</cd:artist>
  <cd:country>USA</cd:country>
  <cd:company>Columbia</cd:company>
  <cd:price>10.90</cd:price>
  <cd:year>1985</cd:year>
</rdf:Description>

<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Hide your heart">
  <cd:artist>Bonnie Tyler</cd:artist>
  <cd:country>UK</cd:country>
  <cd:company>CBS Records</cd:company>
  <cd:price>9.90</cd:price>
  <cd:year>1988</cd:year>
</rdf:Description>

...

</rdf:RDF>
```

以上是一个 RDF 实例：

- 第一行是 XML 声明
- XML 声明后是 RDF 文档的根元素 `<rdf:RDF>`，命名空间 `xmlns:rdf` 和 `xmlns:cd` 
- `<rdf:Description>` 元素包含了对被 `rdf:about` 属性标识的资源的描述
- `cd:artist` 、`cd:country` 、`cd:company` 等是此资源的属性

> 备注：[W3C 的 RDF 验证器](<https://www.w3.org/RDF/Validator/>) 

## RDF 主要元素

RDF 的主要元素是根元素 `<RDF>` 以及可表示某个资源的 `<Description>` 元素。

**主元素 `<rdf:RDF>`**

`<rdf:RDF>`  是 RDF 文档的根元素。它把 XML 文档定义为一个 RDF 文档。它也包含了对 RDF 命名空间的引用。

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">
...Description goes here...
</rdf:RDF> 
```

**主元素 `<rdf:Description>`**

`<rdf:Description>` 元素可通过 `about` 属性标识一个资源。

`<rdf:Description>` 元素可包含描述资源的那些元素。

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:cd="http://www.recshop.fake/cd#">

<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Empire Burlesque">
  <cd:artist>Bob Dylan</cd:artist>
  <cd:country>USA</cd:country>
  <cd:company>Columbia</cd:company>
  <cd:price>10.90</cd:price>
  <cd:year>1985</cd:year>
</rdf:Description>

</rdf:RDF>
```

**属性（property）来定义属性（attribute）**

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:cd="http://www.recshop.fake/cd#">

<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Empire Burlesque"
cd:artist="Bob Dylan" cd:country="USA"
cd:company="Columbia" cd:price="10.90"
cd:year="1985" />

</rdf:RDF>
```

**属性（property）来定义资源（resources）**

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:cd="http://www.recshop.fake/cd#">

<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Empire Burlesque">
  <cd:artist rdf:resource="http://www.recshop.fake/cd/dylan" />
  ...
</rdf:Description>

</rdf:RDF>
```

## RDF 容器

RDF 容器用于描述一组事物。

RDF 容器：`<Bag>`、`<Seq>` 以及 `<Alt>`。

**`<rdf:Bag>` 元素**

用于描述一个规定为无序的值的列表。

可包含重复的值。

```xml
<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Beatles">
  <cd:artist>
    <rdf:Bag>
      <rdf:li>John</rdf:li>
      <rdf:li>Paul</rdf:li>
      <rdf:li>George</rdf:li>
      <rdf:li>Ringo</rdf:li>
    </rdf:Bag>
  </cd:artist>
</rdf:Description>
```

**`<rdf:Seq>` 元素**

用于描述一个规定为有序的值的列表（比如一个字母顺序的排序）。

可包含重复的值。

```xml
<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Beatles">
  <cd:artist>
    <rdf:Seq>
      <rdf:li>George</rdf:li>
      <rdf:li>John</rdf:li>
      <rdf:li>Paul</rdf:li>
      <rdf:li>Ringo</rdf:li>
    </rdf:Seq>
  </cd:artist>
</rdf:Description>
```

**`<rdf:Alt>` 元素**

用于一个可替换的值的列表（用户仅可选择这些值的其中之一）。

```xml
<rdf:Description 
rdf:about="http://www.recshop.fake/cd/Beatles">
  <cd:format>
    <rdf:Alt>
      <rdf:li>CD</rdf:li>
      <rdf:li>Record</rdf:li>
      <rdf:li>Tape</rdf:li>
    </rdf:Alt>
  </cd:format>
</rdf:Descriptio>
```

## RDF 集合

容器规定了所包含的资源为成员，但没有规定其他的成员是不被允许的，即无法关闭一个容器。

RDF 集合用于描述仅包含指定成员的组。

集合是通过属性 `rdf:parseType="Collection"` 来描述的。 

```xml
<rdf:Description 
rdf:about="http://recshop.fake/cd/Beatles">
  <cd:artist rdf:parseType="Collection">
    <rdf:Description rdf:about="http://recshop.fake/cd/Beatles/George"/>
    <rdf:Description rdf:about="http://recshop.fake/cd/Beatles/John"/>
    <rdf:Description rdf:about="http://recshop.fake/cd/Beatles/Paul"/>
    <rdf:Description rdf:about="http://recshop.fake/cd/Beatles/Ringo"/>
  </cd:artist>
</rdf:Description>
```

## RDF Schema

RDF 本身并不能定义某些类和属性，它们需要用 RDF 的描述语言 RDF Schema 来定义。

RDF Schema (RDFS) 是对 RDF 的一种扩展。

RDF Schema 不提供实际的应用程序专用的类和属性，而是提供了描述应用程序专用的类和属性的框架。

一个 RDFS 实例：

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
xml:base="http://www.animals.fake/animals#">

<rdf:Description rdf:ID="animal">
  <rdf:type rdf:resource="http://www.w3.org/2000/01/rdf-schema#Class"/>
</rdf:Description>

<rdf:Description rdf:ID="horse">
  <rdf:type rdf:resource="http://www.w3.org/2000/01/rdf-schema#Class"/>
  <rdfs:subClassOf rdf:resource="#animal"/>
</rdf:Description>

</rdf:RDF>
```

可以通过使用 `rdfs:Class` 取代 `rdf:Description`，并去掉 `rdf:type` 信息，简写以上实例：

```xml
<?xml version="1.0"?>

<rdf:RDF 
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
xml:base="http://www.animals.fake/animals#">

<rdfs:Class rdf:ID="animal" />

<rdfs:Class rdf:ID="horse">
  <rdfs:subClassOf rdf:resource="#animal"/>
</rdfs:Class>

</rdf:RDF>
```

## RDF 都柏林核心

都柏林核心元数据倡议，英文全称为 Dublin Core Metadata Initiative，创建了一些供描述文档的预定义属性。

| 属性        | 定义                                       |
| :---------- | :----------------------------------------- |
| Contributor | 一个负责为资源内容作出贡献的实体(如作者)。 |
| Coverage    | 资源内容的氛围或作用域                     |
| Creator     | 一个主要负责创建资源内容的实体。           |
| Format      | 物理或数字的资源表现形式。                 |
| Date        | 在资源生命周期中某事件的日期。             |
| Description | 对资源内容的说明。                         |
| Identifier  | 一个对在给定上下文中的资源的明确引用       |
| Language    | 资源智力内容所用的语言。                   |
| Publisher   | 一个负责使得资源内容可用的实体             |
| Relation    | 一个对某个相关资源的引用                   |
| Rights      | 有关保留在资源之内和之上的权利的信息       |
| Source      | 一个对作为目前资源的来源的资源引用。       |
| Subject     | 一个资源内容的主题                         |
| Title       | 一个给资源起的名称                         |
| Type        | 资源内容的种类或类型。                     |

都柏林核心属性在一个 RDF 文档中的使用：

```xml
<?xml version="1.0"?>

<rdf:RDF
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:dc= "http://purl.org/dc/elements/1.1/">

<rdf:Description rdf:about="http://www.w3cschool.cn">
  <dc:description>W3Cschool - 奔跑吧！</dc:description>
  <dc:publisher>Refsnes Data as</dc:publisher>
  <dc:date>2008-09-01</dc:date>
  <dc:type>Web Development</dc:type>
  <dc:format>text/html</dc:format>
  <dc:language>en</dc:language>
</rdf:Description>

</rdf:RDF>
```

## OWL 简介

[W3C OWL 简单介绍](<https://www.w3cschool.cn/rdf/rdf-owl.html>) 

## RDF 参考手册

[查看 RDF 参考手册](<https://www.w3cschool.cn/rdf/rdf-reference.html>) 