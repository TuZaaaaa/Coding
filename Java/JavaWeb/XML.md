# XML

## 什么是 XML

xml 是可扩展的标记性语言



## xml 的作用？

xml 的主要作用有： 

1. 用来保存数据，而且这些数据具有自我描述性 

2. 它还可以做为项目或者模块的配置文件 

3. 还可以做为网络传输数据的格式（现在 JSON 为主）

   

## 语法

1. 文档声明

2. xml 注释 

3. 元素（标签）

4. xml 属性

5. 语法规则

   

### 1.文档声明

```xml
<?xml version="1.0" encoding="UTF-8"?> xml 声明。
<!-- xml 声明 version 是版本的意思 encoding 是编码 -->
```

而且这个 <?xml 要连在一起写，否则会有报错

| 属性                | 含义                                   |
| ------------------- | -------------------------------------- |
| version             | 版本号                                 |
| encoding            | xml 的文件编码                         |
| standalone="yes/no" | 表示这个 xml 文件是否是独立的 xml 文件 |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- xml 声明 version 是版本的意思 encoding 是编码 -->
<books> <!-- 这是 xml 注释 -->
	<book id="SN123123413241"> <!-- book 标签描述一本图书 id 属性描述 的是图书 的编号 -->
		<name>java 编程思想</name> <!-- name 标签描述 的是图书 的信息 -->
		<author>华仔</author> <!-- author 单词是作者的意思 ，描述图书作者 -->
		<price>9.9</price> <!-- price 单词是价格，描述的是图书 的价格 -->
	</book>
	<book id="SN12341235123"> <!-- book 标签描述一本图书 id 属性描述 的是图书 的编号 -->
		<name>葵花宝典</name> <!-- name 标签描述 的是图书 的信息 -->
		<author>班长</author> <!-- author 单词是作者的意思 ，描述图书作者 -->
		<price>5.5</price><!-- price 单词是价格，描述的是图书 的价格 -->
	</book>
</books>
```



### 2.注释

```xml
html 和 XML 注释 一样 : <!-- html 注释 -->
```



### 3.元素（标签）

#### XML元素

XML元素指的是从（且包括）开始标签直到（且包括）结束标签的部分

元素可包含其他元素、文本或者两者的混合物，元素也可以拥有属性

#### 命名规则

- 名称可以含字母、数字以及其他的字符
- 名称不能以数字或者标点符号开始
- 名称不能包含空格



### 4.XML属性

xml 的标签属性和 html 的标签属性是非常类似的，**属性可以提供元素的额外信息** 

在标签上可以书写属性： 一个标签上可以书写多个属性

**每个属性的值必须使用 引号 引起来** 的规则和标签的书写规则一致



### 5.语法规则

- 所有 XML 元素都须有关闭标签（也就是闭合）
- XML 标签对大小写敏感
- XML 必须正确地嵌套
- XML 文档必须有根元素

> 根元素就是顶级元素， 没有父标签的元素，叫顶级元素
>
> 根元素是没有父标签的顶级元素，而且是唯一一个才行

- XML 的属性值须加引号
- 文本区域（CDATA 区）

> CDATA 语法可以告诉 xml 解析器，我 CDATA 里的文本内容，只是纯文本，
>
> 不需要 xml 语法解析 CDATA 格式：

```xml
<![CDATA[ 这里可以把你输入的字符原样显示，不会解析 xml ]]
```



## 解析技术介绍

### dom4j

#### 目录介绍

doc 是文档目录 第三方类库学习文档

lib 是 dom4j 需要依赖其他第三方的类库

src dom4j 源码目录



#### 编程步骤

第一步： 先加载 xml 文件创建 Document 对象

第二步：通过 Document 对象拿到根元素对象

第三步：通过根元素.elelemts(标签名); 可以返回一个集合，这个集合里放着。所有你指定的标签名的元素对象

第四步：找到你想要修改、删除的子元素，进行相应在的操作

第五步，保存到硬盘上



> 基础示范

```java
package web;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.junit.jupiter.api.Test;

import java.util.List;

/**
 * @author Rabbit
 */
public class Dom4jTest {
    public static void main(String[] args) {
    }

    @Test
    public void test1() {
        //创建一个 SAXReader 输入流，去读取 xml 配置文件，生成 Document 对象
        SAXReader reader = new SAXReader();
        Document document = null;
        try {
            document = reader.read("src/web/books.xml");
        } catch (DocumentException e) {
            e.printStackTrace();
        }
        System.out.println(document);
    }

    @Test
    public void test2() throws DocumentException {
        //1读取 books.xml 文件，获取 Document 对象
        SAXReader reader = new SAXReader();
        Document document = reader.read("src/web/books.xml");

        //2.通过 Document 对象获取根元素
        Element rootElement = document.getRootElement();

        //3.通过根元素获取 book 标签对象
        List<Element> books = rootElement.elements("book");

        //4.遍历处理每个 book 标签转换为 Book 类
        for (Element book : books) {
            Element name = book.element("name");
            //asXML 将标签对象转换为标签字符串
            String s = name.asXML();
            System.out.println(s);
            String bookName = name.getText();
            System.out.println(bookName);
            //getText() 获取标签中的文本内容
            Element author = book.element("author");
            String authorName = author.getText();
            System.out.println(authorName);
            // elementText() 直接获取标签的内容
            String price = book.elementText("price");
            System.out.println(price);
            //5.打印 Book 对象
            System.out.println(new Book(bookName,authorName,Double.parseDouble(price)));
            System.out.println();
        }
    }
}
```

