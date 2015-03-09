---
layout: post
title: xml文件的序列化和解析
description: xml文件的序列化和解析
date: 2015-03-08
category: Android
---

## 序列化数据到本地xml文件

用到的是XmlSerializer类，获得序列化对象之后，我们就可以来将所需要的数据依次写入xml文件。其中有几个重要方法：    

**setOutput：**指定输出位置和编码方法      
**startDocument**和**xmlSerializer.endDocument()：**标识xml文件的开头和结尾      
**startTag**和**xmlSerializer.endTag：**具体的内部小标签的开始和结尾       
**attribute：**在节点中追加属性，例如\<person id="@@"\> ....\</person>          
**text：**在节点后的属性，例如\<name\>@@ \</name\>

{% highlight java %} 
private void writeXmlToLocal() {
		//获取一些数据，此处略去此方法
		//Person有三个数据：int id,int age,String name
		List<Person> personList = this.getPersonList();
		// 获得序列化对象
		XmlSerializer xmlSerializer = Xml.newSerializer();

		File file = new File(Environment.getExternalStorageDirectory(),
				"person.xml");
		FileOutputStream fos = null;
		try {
			fos = new FileOutputStream(file);
			// 指定序列化对象的输出位置（一个输出流来表示）和编码方式
			xmlSerializer.setOutput(fos, "utf-8");

			// xml头部，就是这种<?xml version='1.0' encoding='utf-8' standalone='yes'?>
			//true代表是否standalone
			xmlSerializer.startDocument("utf-8", true);
			//命名空间此处没有，设为null
			xmlSerializer.startTag(null, "persons");
			for (Person person : personList) {
				// 开始把人依次写入
				xmlSerializer.startTag(null, "person");
				xmlSerializer.attribute(null, "id",
						String.valueOf(person.getId())); // 写出来是：<person id="@@">

				xmlSerializer.startTag(null, "name");
				xmlSerializer.text(person.getName()); // 写出来是：<name>@@</name>
				xmlSerializer.endTag(null, "name");

				xmlSerializer.startTag(null, "age");
				xmlSerializer.text(person.getAge() + ""); // 可以String.valueOf(person.getAge())
				xmlSerializer.endTag(null, "age");

				xmlSerializer.endTag(null, "person");
			}
			xmlSerializer.endTag(null, "persons");

			// 结束，没有类似于头部那样显式的东西，就是来标识文件的结束
			xmlSerializer.endDocument();
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				fos.close();
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
{% endhighlight %}

- - -

## 解析本地xml文件

用到的是XmlPullParser类，获得XmlPullParser对象之后，就可以选定指定的xml文件进行解析。其中的几个重要方法：

**setInput：**指定解析的文件(通过一个输入流)和编码方式      
**getEventType：**获得事件类型，即标签类型，主要有：START_DOCUMENT，START_TAG等。从而可以通过判断标签类型来进一步读取数据         
**getName：**获取节点名称，person，name等具体的名字            
**getAttributeValue：**与上面的attribute对应，从节点中读取信息      
**nextText：**与上面的text对应，用于在获取完节点名称之后，获取其中的信息               
**next：**获取下一个事件类型

{% highlight java %} 
private List<Person> parseXmlFromLocal() {
	// 获得pull解析器对象
	XmlPullParser newPullParser = Xml.newPullParser();
	List<Person> personsList = null;
	//可以重复利用，避免创建多个person对象
	Person person = null;  
	File file = new File(Environment.getExternalStorageDirectory(),
			"person.xml");
	FileInputStream fis = null;
	try {
		fis = new FileInputStream(file);
		// 指定解析的文件和编码方式
		newPullParser.setInput(fis, "utf-8");
	
		// 获得事件类型(也就是标签类型的样子)
		int eventType = newPullParser.getEventType();
		//Returns the type of the current event (START_TAG, END_TAG, TEXT, etc.)
		
		while (eventType != newPullParser.END_DOCUMENT) {
			String tagName = newPullParser.getName(); // 获得节点名称
			// persons,age等
			switch (eventType) {
			case XmlPullParser.START_TAG: // 检测到是开始标签，各种：persons,name等
					if ("persons".equals(tagName)) {
					personsList = new ArrayList<Person>();
				} else if ("person".equals(tagName)) {
					person = new Person();
					person.setId(Integer.valueOf(newPullParser
							.getAttributeValue(null, "id")));
				} else if ("name".equals(tagName)) {
					person.setName(newPullParser.nextText());
				} else if ("age".equals(tagName)) {
					person.setAge(Integer.valueOf(newPullParser.nextText()));
				}
				break;
			case XmlPullParser.END_TAG:
				if ("person".equals(tagName)) {
					personsList.add(person);
				}
				break;
			default:
				break;
			}

			//得到下一个事件类型
			eventType = newPullParser.next(); 
		}
		return personsList;
	} catch (Exception e) {
		// TODO: handle exception
		return null;
	} finally {
		try {
			fis.close();
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
{% endhighlight %}
