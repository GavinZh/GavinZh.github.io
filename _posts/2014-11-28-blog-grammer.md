---
layout: post
title: 博客语法
description: 对各种语法逐一测试
date: 2014-11-28
category: blog
---

**说明：本文仅是对写文章的一些语法进行了测试，无实际内容...下面开始**

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

测试行代码
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
|row2text1|row2text2|row2text3|row2text4
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

**测试图片**（无需下空一行,可以是本地或者是图片的网址）
![Taylor](/images/other/taylor-01.jpg)

**贴网址相关**

Windows版的下载地址在这里：[真正的显示](http://code.google.com/p/msysgit/downloads/list "放上去会显示此提示")。
<ul>
	<li> a <a href="https://www.dnspod.cn/Support">使用html语法</a></li>
	<li> b </li>
</ul>
**注：**由于列表用的是html语法，所以在列表中需要用到html语法来贴网址

通过辨识标签（例如1,2,.. a,b...）来定义，然后在下边解释。标签可以是空的，如下面的Github

[百度][1]   [淘宝][2]   [Github][]    
[FrontMatter](http://jekyllrb.com/docs/frontmatter/){:target="_blank"}

*  [百度][1]

**注：**若是列表用的是mk语法，就可以里面这样贴标签喽


[Github]: http://github.com "Github（前面冒号处至少一个以上空格或tab）"
[1]: (http://www.baidu.com  "跳往百度"){:target="_blank"}
[2]: http://www.taobao.com  "跳往淘宝"