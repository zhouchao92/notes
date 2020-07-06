---
title: Java--xml文件解析
tags:
  - xml解析
categories:
  - java
  - notes
abbrlink: 7fb4a625
date: 2020-04-26 10:59:01
---


##### xml解析

xml解析方式有DOM、SAX、Stax三种方式

树结构

- DOM: Document Object Model 文档对象模型，擅长(小规模)读/写

流结构

- SAX: Simple API for XML 流机制解释器(推模式)，擅长读
- Stax: The Streaming API for XML 流机制解释器(拉模式)，擅长读， JDK 6 引入

<!--more-->

###### DOM

DOM 是 W3C 处理 XML 的标准 API

- 直观易用
- 其处理方式是将 XML 整个作为类似树结构的方式读入内存中以 便操作及解析，方便修改
- 解析大数据量的 XML 文件，会遇到内存泄露及程序崩溃的风险

> DOM 类
>
> - DocumentBuilder 解析类，parse方法
> - Node 节点主接口，getChildNodes返回一个NodeList
> - NodeList 节点列表，每个元素是一个Node
> - Document 文档根节点 
> - Element 标签节点元素 (每一个标签都是标签节点) 
> - Text节点 (包含在XML元素内的，都算Text节点) 
> - Attr节点(每个属性节点)

> 读取xml文件：fonts.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<fonts>
    <font id="0">
        <name>宋体</name>
        <size>18</size>
    </font>
    <font id="1">
        <name>微软雅黑</name>
        <size>12</size>
    </font>
    <font id="2">
        <name>正楷</name>
        <size>16</size>
    </font>

</fonts>
```

> DomReader.java

```java
public class DomReader {
    public static void main(String[] args) {
        recursiveTraverse();
        System.out.println("--------------------分割线--------------------");
        traverseBySearch();
    }

    /**
     * 遍历xml文档
     */
    public static void recursiveTraverse() {
        try {
            //采用DOM解析xml文件
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document document = db.parse("fonts.xml");

            //获取所有一级子节点
            NodeList childNodes = document.getChildNodes();
            for (int i = 0; i < childNodes.getLength(); i++) { //1=<fonts>
                Node font = childNodes.item(i);
                NodeList fontList = font.getChildNodes();   //获取所有二级子节点
                for (int j = 0; j < fontList.getLength(); j++) {    //7=4个空格+3*<font>
                    Node fontItem = fontList.item(j);
                    if (font.getNodeType() == Node.ELEMENT_NODE) {  //获取内容
                        NodeList fontChildNodes = fontItem.getChildNodes();
                        for (int k = 0; k < fontChildNodes.getLength(); k++) {  //5=3个空格+<name>+<size>
                            Node fontNode = fontChildNodes.item(k);
                            if (fontNode.getNodeType() == Node.ELEMENT_NODE) {  //获取内容
                                System.out.println(fontNode.getNodeName() + ":" + fontNode.getTextContent());
                            }
                        }
                    }
                }
            }
        } catch (ParserConfigurationException | SAXException | IOException e) {
            e.printStackTrace();
        }
    }

    /**
* 通过标签名查找元素
*/
    public static void traverseBySearch() {
        try {
            //采用DOM解析xml文件
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document document = db.parse("fonts.xml");

            Element rootElement = document.getDocumentElement();
            NodeList nodeList = rootElement.getElementsByTagName("name");

            if (nodeList != null) {
                for (int i = 0; i < nodeList.getLength(); i++) {
                    Element element = (Element) nodeList.item(i);
                    System.out.println(element.getNodeName() + ":" + element.getTextContent());
                }
            }
        } catch (ParserConfigurationException | IOException | SAXException e) {
            e.printStackTrace();
        }
    }
}
```

> 写xml文件

```java
public class DomWriter {
    public static void main(String[] args) {
        try {
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document document = db.newDocument();   //创建document结点
            if (document != null) {
                //通过document结点创建元素
                Element docx = document.createElement("document");
                Element element = document.createElement("element");
                element.setAttribute("type", "paragraph");  //设置属性
                element.setAttribute("alignment", "left");

                Element object = document.createElement("object");
                object.setAttribute("type", "text");

                Element text = document.createElement("text");
                text.appendChild(document.createTextNode("This is text node")); //元素赋值

                Element bold = document.createElement("bold");
                bold.appendChild(document.createTextNode("true"));  //元素赋值

                object.appendChild(text);   //挂载
                object.appendChild(bold);
                element.appendChild(object);
                docx.appendChild(element);
                document.appendChild(docx);

                TransformerFactory transformerFactory = TransformerFactory.newInstance();
                Transformer transformer = transformerFactory.newTransformer();

                DOMSource source = new DOMSource(document); //将结点转化成dom资源

                //创建目标文件
                File file = new File("dom.xml");
                StreamResult streamResult = new StreamResult(file);
                //将xml内容写入文件中
                transformer.transform(source, streamResult);
            }
        } catch (ParserConfigurationException | TransformerException e) {
            e.printStackTrace();
        }
    }
}
```

> 生成的xml文件：dom.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<document>
    <element alignment="left" type="paragraph">
        <object type="text">
            <text>This is text node</text>
            <bold>true</bold>
        </object>
    </element>
</document>
```

###### SAX

Simple API for XML

- 采用事件/流模型来解析 XML 文档，更快速、更轻量。 
- 有选择的解析和访问，不像 DOM 加载整个文档，内存要求较低。 – SAX 对 XML 文档的解析为一次性读取，不创建/不存储文档对象，很难同时访问文档中的多处数据。 
- 推模型 当它每发现一个节点就引发一个事件，而我们需要编写 这些事件的处理程序。
- 关键类 DefaultHandler

> SAXHandler.java

```java
/**
* 处理xml文档内容
*/
class SAXHandler extends DefaultHandler {

    private List<String> nameList;

    private boolean isName = false;

    public List<String> getNameList() {
        return nameList;
    }

    //xml文档加载时
    @Override
    public void startDocument() throws SAXException {
        System.out.println("start document");
        nameList = new ArrayList<String>();
    }

    //xml文档解析结束
    @Override
    public void endDocument() throws SAXException {
        System.out.println("end document");
    }

    //访问某一个元素
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        System.out.println("start element");
        if (qName.equals("name")) {
            isName = true;
        }
    }

    //结束访问某一个元素
    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        System.out.println("end element");
        if (isName) {
            isName = false;
        }
    }

    //访问元素正文
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        if (isName) {
            String name = new String(ch, start, length);
            nameList.add(name);
        }
    }
}

```

> XmlSAXReader.java 测试读文件 fonts.xml

```java
public class XmlSAXReader {
    public static void main(String[] args) throws SAXException, IOException {
        XMLReader parser = XMLReaderFactory.createXMLReader();
        SAXHandler handler = new SAXHandler();
        parser.setContentHandler(handler);
        parser.parse("fonts.xml");
        System.out.println(handler.getNameList());
    }
}
```

###### Stax

Streaming API for XML

- 流模型中的拉模型
- 在遍历文档时，会把感兴趣的部分从读取器中拉出，不需要引发 事件，允许我们选择性地处理节点。这大大提高了灵活性，以及整体效率
- 两套处理API 
  - 基于指针的API， XMLStreamReader
  - 基于迭代器的API，XMLEventReader

> 测试读取xml文件
>
> 事件模式 和 流模式

```java
public class StaxReader {
    public static void main(String[] args) {
        staxReaderByEvent();
        System.out.println("-------------分割线-------------");
        staxReaderByStream();
    }

    /**
     * 事件模式
     */
    private static void staxReaderByEvent() {
        String xmlFile = "fonts.xml";
        XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();
        boolean isName = false;
        try {
            //创建基于迭代器的事件读取器对象
            XMLEventReader xmlEventReader = xmlInputFactory.createXMLEventReader(new FileReader(xmlFile));
            //遍历event迭代器
            while (xmlEventReader.hasNext()) {
                XMLEvent xmlEvent = xmlEventReader.nextEvent();
                //事件对象是元素的开始
                if (xmlEvent.isStartElement()) {
                    //转换为开始元素事件对象
                    StartElement startElement = xmlEvent.asStartElement();
                    if (startElement.getName().getLocalPart().equals("name"))
                        isName = true;
                    //获取所有属性
                    Iterator attr = startElement.getAttributes();
                    while (attr.hasNext())
                        System.out.println(attr.next());
                }
                //事件对象是正文
                if (xmlEvent.isCharacters()) {
                    //获取正文内容
                    String data = xmlEvent.asCharacters().getData();
                    //打印内容
                    if (isName && data != null && data.trim().length() != 0)
                        System.out.println(data);
                }
                //事件对象是元素结束
                if (xmlEvent.isEndElement()) {
                    //将事件对象转换成元素结束
                    EndElement endElement = xmlEvent.asEndElement();
                    if (endElement.getName().getLocalPart().equals("name"))
                        isName = false;
                }
            }
            //结束
            xmlEventReader.close();
        } catch (FileNotFoundException | XMLStreamException e) {
            e.printStackTrace();
        }
    }

    /**
     * 流模式
     */
    private static void staxReaderByStream() {
        String xmlFile = "fonts.xml";
        XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();
        XMLStreamReader xmlStreamReader = null;

        try {
            xmlStreamReader = xmlInputFactory.createXMLStreamReader(new FileReader(xmlFile));
        } catch (XMLStreamException | FileNotFoundException e) {
            e.printStackTrace();
        }
        //基于指针的遍历
        try {
            if (xmlStreamReader != null) {
                while (xmlStreamReader.hasNext()) {
                    int event = xmlStreamReader.next();
                    //元素的开始
                    if (event == XMLStreamConstants.START_ELEMENT)
                        //打印<font>的name标签的内容
                        if ("name".equals(xmlStreamReader.getLocalName()))
                            System.out.println("name:" + xmlStreamReader.getElementText());
                }
                //关流
                xmlStreamReader.close();
            }
        } catch (XMLStreamException e) {
            e.printStackTrace();
        }
    }
}
```

###### 其他的第三方库

DOM/SAX/Stax是JDK自带的解析功能

第三方库

- JDOM: [https://www.jdom.org](https://www.jdom.org)
-  DOM4J: [https://dom4j.github.io](https://dom4j.github.io)

第三方库一般都包含DOM,SAX等多种方式解析，是对Java 解析进行封装