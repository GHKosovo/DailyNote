# XML数据

解析`XML`数据，感觉很难，无从下手:speak_no_evil:，其实是因为解析`XML`的[方法](https://www.cnblogs.com/c-xiaohai/p/6796116.html)太多了，感觉都差不多，混合在一块，分不清了:dizzy_face:就看不懂了。

<!--more-->

## 解析xml

我采用的是`dom4`的[方法](http://www.51gjie.com/java/739.html)解析`XML`

```java
SAXReader saxReader = new SAXReader();
Document document = saxReader.read(new File("students.xml"));
// 获取根元素
Element root = document.getRootElement();
// 获取所有子元素
List<Element> childList = root.elements();
// 把xml信息转化为hashmap
HashMap<String,String> hashMap = new HashMap<String, String>();
for (Element element : elementsList) {
    hashMap.put(element.getName(), element.getText());
}
```

<hr>

## 序列化/反序列化xml

1. 创建`XStream` 对象

```java
XStream xstream = new XStream(new StaxDriver());
```

2. 序列化对象到`XML`

```java
// Object to XML Conversion
String xml = xstream.toXML(student);
```

3. 反序列化`XML`获得对象。

```java
// XML to Object Conversion
Student student1 = (Student) xstream.fromXML(xml)
```

> 这里生成的XML是包含`<?xml version="1.0" encoding="UTF-8" ?>`前缀的xml文本

<hr>

## _微信的XML格式_
微信的`XML`回复信息，某些文本需要添加`<![CDATA['text']]`，网友提供的方法如下，但是我还看不懂啊:joy:

```java
//自定义xml对象格式
private static XStream xStream = new XStream(new XppDriver() {
	public HierarchicalStreamWriter createWriter(Writer out) {
		return new PrettyPrintWriter(out) {
			boolean cdata = true;
			
			public void startNode(String name, Class clazz) {
				super.startNode(name, clazz);
			}
			
			protected void writeText(QuickWriter writer,String text) {
				if(cdata) {
					writer.write("<![CDATA[");
					writer.write(text);
					writer.write("]]>");
				}else {
					writer.write(text);
				}
			}
		};	
	}		
});
```

结尾