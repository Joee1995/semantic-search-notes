# W3Cschool - XML 教程笔记

教程：[W3Cschool - XML 教程](<https://www.w3cschool.cn/xml/?>) 

[TOC]

## XML 教程

**XML 示例**

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

> 备注：[W3Cschool 在线实例测试](<https://www.w3cschool.cn/xml/xml-examples.html>) 

### XML 简介

**什么是 XML ？**

- XML，可扩展的标识语言（eXtensible Markup Language），其先驱是SGML和[HTML](https://www.w3cschool.cn/html)。

- XML 是对 HTML 的补充。

- XML 的设计宗旨是传输数据，而不是显示数据。

- XML 标签没有被预定义。需要自行定义标签。

- XML 被设计为具有自我描述性。

- XML 是独立于软件和硬件的信息传输工具。

**XML & HTML**

- XML 被设计用来传输和存储数据。

- HTML 被设计用来显示数据。

> 提示：XML 是可扩展标记语言，HTML 是超文本标记语言。

**XML 作用**

- 丰富文件（Rich Documents）-自定文件描述并使其更丰富；
- 属于文件为主的XML技术应用；
- 标记是用来定义一块数据应该如何呈现；
- 解释数据（Metadata）- 描述其它文件或在线信息；
- 属于数据为主的XML技术应用；
- 标记是用来说明一块资料的意义；
- 组态档案（Configuration Files）- 描述软件的组态参数。

**XML 不会做任何事**

XML 被设计用来结构化、存储以及传输信息。

笔记开头提供了一个示例，该 XML 文档没有做任何事情，仅仅是包装在 XML 标签中的纯粹的信息。我们需要编写软件或者程序，才能传送、接收和显示出这个文档。

**XML 可以自定义标签**

XML 语言没有预定义的标签。

XML 允许创作者定义自己的标签和自己的文档结构。

**XML 不是 HTML 的替代**

XML 是对 HTML 的补充。

XML 是独立于软件和硬件的信息传输工具。

### XML 用途

**XML 将数据从 HTML 分离**

如果要在 HTML 文档中显示动态数据，那么每当数据改变时将花费大量的时间来编辑 HTML。

通过 XML，数据能够存储在独立的 XML 文件中。这样就可以专注于使用 HTML/[CSS](https://www.w3cschool.cn/css/css-tutorial.html) 进行显示和布局，并确保修改底层数据不再需要对 HTML 进行任何的改变。

通过使用几行 [JavaScript](https://www.w3cschool.cn/javascript/js-tutorial.html) 代码，就可以读取一个外部 XML 文件，并更新网页的数据内容。

**XML 简化数据共享**

在真实的世界中，通常使用各种不兼容的格式来存储数据。

XML 数据以纯文本格式进行存储，提供了一种独立于软件和硬件的数据存储方法。

这让共享数据变得更加容易。

**XML 简化数据传输**

由于可以通过各种不兼容的应用程序来读取数据，以 XML 交换数据降低了数据传输的复杂性。

**XML 简化平台变更**

XML 数据以文本格式存储。这使得 XML 在不损失数据的情况下，更容易扩展或升级到新的操作系统、新的应用程序或新的浏览器。

**XML 使数据更有用**

不同的应用程序都能够访问您的数据，不仅仅在 HTML 页中，也可以从 XML 数据源中进行访问。

通过 XML，您的数据可供各种阅读设备使用

**XML 用于创建新的互联网语言**

很多新的互联网语言是通过 XML 创建的。

一些实例：

- XHTML
- 用于描述可用的 Web 服务 的 WSDL
- 作为手持设备的标记语言的 WAP 和 WML
- 用于新闻 feed 的 RSS 语言
- 描述资本和本体的 RDF 和 OWL
- 用于描述针对 Web 的多媒体 的 SMIL

### XML 树结构

XML 文档形成了一种树结构，它从"根部"开始，然后扩展到"枝叶"。

XML 文档必须包含**根元素**，该元素是所有其他元素的父元素。

笔记开头提供了一个 XML 文档示例：

- 第一行是 XML 声明。定义 XML 版本和使用编码

- 第二行描述文档的根元素（ `<note>` 表明本文档是个便签）

- 接下来几行描述根的 4 个子元素

- 最后一行定义根元素的结尾

### XML 语法

**所有的 XML 元素都必须有一个关闭标签**

**XML 标签对对大小写敏感**

**XML 标签必须正确嵌套**

**XML 文档必须有根元素**

**XML 属性值必须加引号**

```xml
<!-- 错误 -->
<note date=12/11/2007></note>
<!-- 正确 -->
<note date="12/11/2007"></note>
```

**实体引用**

在 XML 中，一些字符拥有特殊的意义。

如果把字符 "<" 放在 XML 元素中，会发生错误，因为解析器会把它当作新元素的开始。

为了避免这种错误，使用**实体引用**来代替这些字符：

```xml
<!-- 错误 -->
<message>if salary < 1000 then</message>
<!-- 正确 -->
<message>if salary &lt; 1000 then</message>
```

XML 中 5 个预定义实体引用：

| 引用     | 符号 | 说明           |
| -------- | ---- | -------------- |
| `&lt;`   | <    | less than      |
| `&gt;`   | >    | greater than   |
| `&amp;`  | &    | ampersand      |
| `&apos;` | '    | apostrophe     |
| `&quot;` | "    | quotation mark |

> 备注：在 XML 中，只有字符 "<" 和 "&" 确实是非法的。">" 是合法的，但是用实体引用来代替它是一个好习惯。

**XML 中的注释**

```xml
<!-- This is a comment -->
```

**XML 空格会保留**

HTML 会把多个连续的空格字符裁减（合并）为一个。

XML 中，文档中的空格不会被删减。

**XML 以 LF 存储换行**

在 Windows 应用程序中，换行通常以一对字符来存储：回车符（CR）和换行符（LF）。

在 Unix 和 Mac OSX 中，换行以一个字符来存储：换行符（LF）。

XML 以 LF 存储换行。

### XML 元素

**什么是 XML 元素？**

XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。

一个元素可以包含：

- 其他元素
- 文本
- 属性
- 混合以上所有

```xml
<bookstore>
<book category="CHILDREN">
<title>Harry Potter</title>
<author>J K. Rowling</author>
<year>2005</year>
<price>29.99</price>
</book>
<book category="WEB">
<title>Learning XML</title>
<author>Erik T. Ray</author>
<year>2003</year>
<price>39.95</price>
</book>
</bookstore>
```

**XML 命名规则**

XML 元素必须遵循以下命名规则：

- 名称可以包含字母、数字以及其他的字符
- 名称不能以数字或者标点符号开始
- 名称不能以字母 xml（或者 XML、Xml 等等）开始
- 名称不能包含空格

除此之外，可使用任何名称，没有保留的字词。

**最佳命名习惯**

使名称具有描述性。使用下划线的名称也很不错：<first_name>、<last_name>。

名称应简短和简单，比如：<book_title>，而不是：<the_title_of_the_book>。

避免 "-" 字符。如果您按照这样的方式进行命名："first-name"，一些软件会认为您想要从 first 里边减去 name。

避免 "." 字符。如果您按照这样的方式进行命名："first.name"，一些软件会认为 "name" 是对象 "first" 的属性。

避免 ":" 字符。冒号会被转换为命名空间来使用（稍后介绍）。

XML 文档经常有一个对应的数据库，其中的字段会对应 XML 文档中的元素。有一个实用的经验，即使用数据库的命名规则来命名 XML 文档中的元素。

在 XML 中，éòá 等非英语字母是完全合法的，不过需要留意，您的软件供应商不支持这些字符时可能出现的问题。

### XML 属性

在 XML 中，属性提供有关元素的额外信息，通常是不属于数据组成部分但在处理这个元素时很重要的信息。例如以下 `type` 属性对于处理 `file` 元素是很有用的：

```xml
<file type="gif">computer.gif</file>
```

**XML 属性必须加引号**

```xml
<person sex="female">
<person sex='female'>
<gangster name='George "Shotgun" Ziegler'>
<gangster name="George &quot;Shotgun&quot; Ziegler">
```

**XML 元素 vs. 属性**

```xml
<!-- 属性 -->
<person sex="female">
</person>

<!-- 元素 -->
<person>
<sex>female</sex>
</person>
```

> 备注：属性很便利，但应尽量避免使用属性，如果信息感觉很像数据，就用元素吧

**三种方式**

第一种：属性方式

```xml
<note date="10/01/2008">
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

第二种：元素方式

```xml
<note>
<date>10/01/2008</date>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

第三种：扩展元素方式

```xml
<note>
<date>
<day>10</day>
<month>01</month>
<year>2008</year>
</date>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

**避免 XML 属性？**

因使用属性而引起的一些问题：

- 属性不能包含多个值（元素可以）
- 属性不能包含树结构（元素可以）
- 属性不容易扩展（为未来的变化）

属性难以阅读和维护。请尽量使用元素来描述数据，而仅使用属性来提供与数据无关的信息。

**合理使用 XML 属性**

一个示例：

```xml
<messages>
<note id="501">
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
<note id="502">
<to>Jani</to>
<from>Tove</from>
<heading>Re: Reminder</heading>
<body>I will not</body>
</note>
</messages>
```

> 备注：以上 `id` 属性仅作为标识区分不同便签，并不是便签的数据组成部分。

**重要：元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素！**

### XML DTD

DTD 是 Document Type Definition 的英文缩写，中文称为“文档类型定义”。

**XML 验证**

拥有正确语法的 XML 被称为"形式良好"的 XML。

通过 DTD 验证的XML是"合法"的 XML。

> 备注：[W3C 的 XML 验证器](<https://www.w3cschool.cn/xml/xml-validator.html>) 

**验证 XML 文档**

“合法”的 XML 首先要是"形式良好"的 XML，然后还要符合 DTD 规则。

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE note SYSTEM "Note.dtd">
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

> 备注：`DOCTYPE` 声明是对外部 DTD 文件的引用。

**XML DTD**

[DTD](<https://www.w3cschool.cn/dtd/>) 的目的是定义 XML 文档的结构。

```xml-dtd
<!DOCTYPE note
[
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
]>
```

**XML Schema**

W3C 支持一种基于 XML 的 DTD 代替者，它名为 [XML Schema](<https://www.w3cschool.cn/xmlschema/>) 

```xml 
<xs:element name="note">

<xs:complexType>
<xs:sequence>
<xs:element name="to" type="xs:string"/>
<xs:element name="from" type="xs:string"/>
<xs:element name="heading" type="xs:string"/>
<xs:element name="body" type="xs:string"/>
</xs:sequence>
</xs:complexType>

</xs:element>
```

### XML 和 CSS

使用 CSS 样式表格式化 XML 文档。

一个示例，第二行将 XML 文档链接到 CSS 文件。

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<?xml-stylesheet type="text/css" href="cd_catalog.css"?>
<CATALOG>
<CD>
<TITLE>Empire Burlesque</TITLE>
<ARTIST>Bob Dylan</ARTIST>
<COUNTRY>USA</COUNTRY>
<COMPANY>Columbia</COMPANY>
<PRICE>10.90</PRICE>
<YEAR>1985</YEAR>
</CD>
...
</CATALOG>
```

> 备注：使用 CSS 格式化 XML 不是常用的方法。W3C 推荐使用 XSLT。

### XML 和 XSLT

[XSLT](<https://www.w3cschool.cn/xslt/>)（eXtensible Stylesheet Language Transformations）是首选的 XML 样式表语言，远比 CSS 更加完善。XSLT 是在浏览器显示 XML 文件之前，先把它转换为 HTML。

**浏览器端转换**

**服务器端转换**

## XML JavaScript

### XMLHttpRequest 对象

XMLHttpRequest 对象用于在后台与服务器交换数据。

XMLHttpRequest 对象可以：

- 在不重新加载页面的情况下更新网页
- 在页面已加载后从服务器请求数据
- 在页面已加载后从服务器接收数据
- 在后台向服务器发送数据

创建 XMLHttpRequest 对象：

```javascript
xmlhttp=new XMLHttpRequest();
```

### XML 解析器

XML 解析器用于检查 XML 文档的适当的格式，并且还可以验证 XML 文档。

所有现代浏览器都有内建的 XML 解析器。

XML 解析器把 XML 文档转换为 XML DOM 对象 - 可通过 JavaScript 操作的对象。

**解析 XML 文档**

将 XML 文档解析到 XML DOM 对象中：

```javascript
if (window.XMLHttpRequest)
{// code for IE7+, Firefox, Chrome, Opera, Safari
xmlhttp=new XMLHttpRequest();
}
else
{// code for IE6, IE5
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.open("GET","books.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML;
```

**解析 XML 字符串**

将 XML 字符串解析到 XML DOM 对象中：

```javascript
txt="<bookstore><book>";
txt=txt+"<title>Everyday Italian</title>";
txt=txt+"<author>Giada De Laurentiis</author>";
txt=txt+"<year>2005</year>";
txt=txt+"</book></bookstore>";

if (window.DOMParser)
{
parser=new DOMParser();
xmlDoc=parser.parseFromString(txt,"text/xml");
}
else // Internet Explorer
{
xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
xmlDoc.async=false;
xmlDoc.loadXML(txt); 
}
```

> 备注：Internet Explorer 使用 loadXML() 方法来解析 XML 字符串，而其他浏览器使用 DOMParser 对象。

### XML DOM

XML 的基础是文档对象模型（DOM）。

[XML DOM](<https://www.w3cschool.cn/xmldom/>)（Document Object Model 文档对象模型）定义了访问和操作文档的标准方法。

XML DOM 把 XML 文档作为树结构来查看。

**加载 XML 文档**

```html
<html>
<body>
<h1>W3Cschools Internal Note</h1>
<div>
<b>To:</b> <span id="to"></span><br />
<b>From:</b> <span id="from"></span><br />
<b>Message:</b> <span id="message"></span>
</div>

<script>
if (window.XMLHttpRequest)
{// code for IE7+, Firefox, Chrome, Opera, Safari
xmlhttp=new XMLHttpRequest();
}
else
{// code for IE6, IE5
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.open("GET","https://www.w3cschool.cn/statics/demosource/note.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML;

document.getElementById("to").innerHTML=
xmlDoc.getElementsByTagName("to")[0].childNodes[0].nodeValue;
document.getElementById("from").innerHTML=
xmlDoc.getElementsByTagName("from")[0].childNodes[0].nodeValue;
document.getElementById("message").innerHTML=
xmlDoc.getElementsByTagName("body")[0].childNodes[0].nodeValue;
</script>

</body>
</html>
```

**加载 XML 字符串**

```html
<html>
<body>
<h1>W3Cschools Internal Note</h1>
<div>
<b>To:</b> <span id="to"></span><br />
<b>From:</b> <span id="from"></span><br />
<b>Message:</b> <span id="message"></span>
</div>

<script>
txt="<note>";
txt=txt+"<to>Tove</to>";
txt=txt+"<from>Jani</from>";
txt=txt+"<heading>Reminder</heading>";
txt=txt+"<body>Don't forget me this weekend!</body>";
txt=txt+"</note>";

if (window.DOMParser)
{
parser=new DOMParser();
xmlDoc=parser.parseFromString(txt,"text/xml");
}
else // Internet Explorer
{
xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
xmlDoc.async=false;
xmlDoc.loadXML(txt);
}

document.getElementById("to").innerHTML=
xmlDoc.getElementsByTagName("to")[0].childNodes[0].nodeValue;
document.getElementById("from").innerHTML=
xmlDoc.getElementsByTagName("from")[0].childNodes[0].nodeValue;
document.getElementById("message").innerHTML=
xmlDoc.getElementsByTagName("body")[0].childNodes[0].nodeValue;
</script>
</body>
</html>
```

### HTML 页面显示 XML 数据

```html
<html>
<body>

<script>
if (window.XMLHttpRequest)
{// code for IE7+, Firefox, Chrome, Opera, Safari
xmlhttp=new XMLHttpRequest();
}
else
{// code for IE6, IE5
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.open("GET","https://www.w3cschool.cn/statics/demosource/cd_catalog.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML; 

document.write("<table border='1'>");
var x=xmlDoc.getElementsByTagName("CD");
for (i=0;i<x.length;i++)
{ 
document.write("<tr><td>");
document.write(x[i].getElementsByTagName("ARTIST")[0].childNodes[0].nodeValue);
document.write("</td><td>");
document.write(x[i].getElementsByTagName("TITLE")[0].childNodes[0].nodeValue);
document.write("</td></tr>");
}
document.write("</table>");
</script>

</body>
</html>
```

### XML 应用程序

[尝试构建小型 XML 应用程序](<https://www.w3cschool.cn/xml/xml-applications.html>) 

## XML 进阶

### XML 命名空间

XML 命名空间是一组唯一的名称，该命名空间是确定的URI（统一资源标识符）。

XML 命名空间提供避免元素命名冲突的方法。

**命名冲突**

在 XML 中，元素名称是由开发者定义的，当两个不同的文档使用相同的元素名时，就会发生命名冲突。

**使用前缀避免命名冲突**

XML 中命名冲突可以通过使用名称前缀避免

```xml
<!-- table：HTML 表格 -->
<h:table>
<h:tr>
<h:td>Apples</h:td>
<h:td>Bananas</h:td>
</h:tr>
</h:table>
<!-- table：家具桌子 -->
<f:table>
<f:name>African Coffee Table</f:name>
<f:width>80</f:width>
<f:length>120</f:length>
</f:table> 
```

**XML 命名空间 - xmlns 属性**

当在 XML 中使用前缀时，一个所谓的用于前缀的**命名空间**必须被定义。

命名空间是在元素的开始标签的 **xmlns 属性**中定义的。

命名空间声明的语法：`xmlns:前缀="URI"`。

默认命名空间声明：`xmlns="URI"`。

命名空间，可以在他们被使用的元素中或者在 XML 根元素中声明。

```xml
<root>
<h:table xmlns:h="http://www.w3.org/TR/html4/">...</h:table>
<f:table xmlns:f="//www.w3cschool.cn/furniture">...</f:table>
</root> 
```

```xml
<root xmlns:h="http://www.w3.org/TR/html4/"
      xmlns:f="//www.w3cschool.cn/furniture">
</root>
```

> 备注：命名空间 URI 不会被解析器用于查找信息，其目的是赋予命名空间一个惟一的名称。

**统一资源标识符**

URI，是 Uniform Resource Identifier 的英文缩写，中文称为“统一资源标识符”。

最常用的 URI 是用来标识因特网域名地址的**统一资源定位器**（URL）。另一个不那么常用的 URI 是**统一资源命名**（URN）。

**实际使用的命名空间**

```xml
xmlns:xsl="http://www.w3.org/1999/XSL/Transform"：

<?xml version="1.0" encoding="ISO-8859-1"?>

<xsl:stylesheet version="1.0"
                xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

<xsl:template match="/">
<html>
<body>
<h2>My CD Collection</h2>
<table border="1">
<tr>
<th align="left">Title</th>
<th align="left">Artist</th>
</tr>
<xsl:for-each select="catalog/cd">
<tr>
<td><xsl:value-of select="title"/></td>
<td><xsl:value-of select="artist"/></td>
</tr>
</xsl:for-each>
</table>
</body>
</html>
</xsl:template>

</xsl:stylesheet>
```

### XML CDATA

XML 文档中的所有文本均会被解析器解析。

只有 CDATA 区段中的文本会被解析器忽略。

**PCDATA - 被解析的字符数据**

XML 解析器通常会解析 XML 文档中所有的文本。

当某个 XML 元素被解析时，其标签之间的文本也会被解析。

解析字符数据（PCDATA）是 XML 解析器解析的文本数据使用的一个术语。

**CDATA - （未解析的）字符数据**

术语 CDATA 是不应该由 XML 解析器解析的文本数据。

CDATA 部分中的所有内容都会被解析器忽略。

CDATA 部分由 `<![CDATA[` 开始，由 `]]>` 结束：

```xml
<script>
<![CDATA[
function matchwo(a,b)
{
if (a < b && a < 0) then
{
return 1;
}
else
{
return 0;
}
}
]]>
</script>
```

> 备注：CDATA 部分不能包含字符串 `]]>`。也不允许嵌套的 CDATA 部分。
>
> 备注：标记 CDATA 部分结尾的 `]]>` 不能包含空格或换行。

## 实例/测验

### XML 实例

[查看 W3Cschool XML 实例演示](<https://www.w3cschool.cn/xml/xml-xmle_examples.html>) 

### XML 测验

[进行 W3Cschool XML 测验](<https://www.w3cschool.cn/xml/xml-xml_quiz.html>) 