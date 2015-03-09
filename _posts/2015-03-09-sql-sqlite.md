---
layout: post
title: 博客语法
description: 对各种语法逐一测试
date: 2014-11-28
category: Blog
---

在java中操作数据库，我们需要首先加载驱动，然后连接数据库，最后才能对数据库进行操作。在Android下也是同样的步骤，不过我们使用Sqlite软件，可以对数据库便捷操作。相关的加载驱动和连接数据库操作无需专门去自己实现，我们只需关注操作数据库本身。

在数据库相关项目中，我们一般分几个包，如下图所示：

![SQL](/images/blog/sqlite-project.PNG)

* 将数据库相关的东西放在**db包**下，操作Android数据库必须先继承SQLiteopenhelper这个抽象类，并实现其中的onCreate和onUpgrade方法，还有就是需要实现一个构造方法；
* 对数据库的操作方法一般放在**dao包**，如增删改查方法。DAO，Data Access Object，代表数据访问对象
* **entities包**存放一些操作的数据库实体，比如学生类，老师类等
* **test包**就是用来进行Junit测试的，一般的测试类都要单独分包

## SQLiteopenhelper类

操作Android数据库必须先继承SQLiteopenhelper这个抽象类：

{% highlight java %} 
public class PersonSQLiteOpenHelper extends SQLiteOpenHelper{

	//实现一个构造函数
	public PersonSQLiteOpenHelper(Context context, String name,
		CursorFactory factory, int version) {
	super(context, name, factory, version);
	}
	//重写onCreate方法
	public void onCreate(SQLiteDatabase db) {
		...
	}
	//重写onUpgrade方法
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		...
	}

}
{% endhighlight %}

下面将三个函数分开阐述

### 构造函数

用于操作管理数据库的，首先继承完后需要实现一个构造函数。直接用到父类的构造方法，一共四个参数：    

* context：创建或者打开数据库
* name：数据库名称
* factory：查询游标结果集，可自己指定，或者是null代表使用默认的游标结果集
* version：版本号，不能小于1，用来检测更新数据库

{% highlight java %} 
//传入的参数中只用到一个context即可，剩下的直接指定
public PersonSQLiteOpenHelper(Context context) {
	super(context, "gavin.db", null, 2);  //name,factory,version(不能小于1)
}
{% endhighlight %}

### onCreate方法

数据库创建时调用此方法，初始化一些元素，比如初始化表。    
需要注意，sqlite数据类型：**无类型（弱类型）**。它可以保存任意类型的数据到要保存的任何表的任何列中。也就是即使实现创建表的时候定义的是integer或者是缺省的，但之后我们可以塞任意类型的数据进去。唯一的例外就是integer primary key字段，只能存储64位的整数。在创建表的时候，为了自己的理解方便，还是注明类型为佳。

{% highlight java %} 
public void onCreate(SQLiteDatabase db) {
	//来创建一张表--person
	String sqlString="create table person(_id integer primary key,name varchar(20),age integer);";
	db.execSQL(sqlString); 
}
{% endhighlight %}

如上所示，onCreate方法传入的参数是SQLiteDatabase，这个对象是SQLiteopenhelper连接数数据库后返回的，通过它我们可以直接对数据库进行操作。也就是说：这儿拿到这个SQLiteDatabase对象db就意味着已经完成了加载驱动+连接数据库。

### onUpgrade方法

数据库更新时(根据版本号来判断)调用此方法，更新数据库内容：增加表、删除表、修改表。

- - - 

## 数据库操作类

如上所述，操作类一般放在Dao包中。

{% highlight java %} 
public class PersonDao {
	private PersonSQLiteOpenHelper mHelper; // 数据库帮助类
	//每次增删改查都得需要用这个帮助对象来获得getWritableDatabase或getReadableDatabase
	//每次只有在调用getReadableDatabase或getWritableDatabase时，数据库才真正创建或链接
	private String sqlsString;

	public PersonDao(Context context) {
		mHelper = new PersonSQLiteOpenHelper(context);
	}
{% endhighlight %}


# 测试 h1（无需下空一行）
一级标题

## 测试 h2
二级标题

### 测试 h3
三级标题

#### 测试 h4
四级标题(最多六个，的之后略去)

测试粗体
**粗体**

测试斜体
*斜体*

测试引用    

> 这里是引用

测试分隔线    

- - -

测试行内代码
`static 段内代码`

测试java代码（下空一行和不空都一样）

{% highlight java %} 
public class TestMain {
  public static void main(String[] args) {
      String str = new String();
      Caller caller = new Caller();
      caller.call(str);
  }
  static class Caller {
      public void call(Object obj) {
          System.out.println("an Object instance in Caller");
      } 
      public void call(String str) {
          System.out.println("a String instance in in Caller");
      }
  }
}
{% endhighlight %}

测试html代码
{% highlight html %}
<link href='http://fonts.googleapis.com/css?family=Spirax' rel='stylesheet' type='text/css'>
<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
{% endhighlight %}

测试xml代码
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
    http://www.springframework.org/schema/context   http://www.springframework.org/schema/context/spring-context-4.0.xsd">
 
    <context:component-scan base-package="com.9leg.java.spring"/>
 
    <bean class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">
        <property name="ignoreUnresolvablePlaceholders" value="true"/>
        <property name="locations">
          <list>
            <value>classpath:spring/config.properties</value>
          </list>
        </property>
      </bean>
</beans>
{% endhighlight %}

测试表格（必须下空一行），不太美观，使用的时候在该列的最长字符处加个空格

|head1|head2|head3|head4
|:---|:---|:---|:---|
|row1text1|rt2|row1text3|row1text4
|row2text1|row2text2&ensp;|row2text3|row2text4
|row3text1|row3text2|row3text3|row3text4
|row4text1|row4text2|row4text3|row4text4

- - - 

测试列表（MK语法,，必须下空一行，*之后加个空格）

* a
* b
* c

测试列表(html语法无需下空一行)
<ul>
    <li>a</li>
    <li>b</li>
    <li>c</li>
    <li>d</li>
</ul>

**测试图片**（下空一行,可以是本地或者是图片的网址）

![Taylor](/images/other/taylor-01.jpg)

**贴网址相关**

Windows版的下载地址在这里：[真正的显示](http://code.google.com/p/msysgit/downloads/list "放上去会显示此提示")。
<ul>
	<li> a <a href="https://www.dnspod.cn/Support" target="_blank">使用html语法---新标签打开</a></li>
	<li> b </li>
</ul>
**注：**由于列表用的是html语法，所以在列表中需要用到html语法来贴网址

通过辨识标签（例如1,2,.. a,b...）来定义，然后在下边解释。标签可以是空的，如下面的Github

[百度][1]{:target="_blank"}   [淘宝][2]   [Github][]    
[FrontMatter](http://jekyllrb.com/docs/frontmatter/){:target="_blank"}

*  [百度][1]

**注：**若是列表用的是mk语法，就可以里面这样贴标签喽


[Github]: http://github.com "Github（前面冒号处至少一个以上空格或tab）"
[1]: http://www.baidu.com  "跳往百度"
[2]: http://www.taobao.com  "跳往淘宝"