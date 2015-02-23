---
layout: post
title: Android下的测试
description: 主要是Junit单元测试
date: 2015-02-22
category: Android
---

## 测试的简单分类

### 是否知道源代码

**黑盒测试**：不关心源代码，只关心程序执行的过程、结果和功能，从输入数据与输出数据的对应关系出发进行测试   
**白盒测试**：根据源代码写测试方法或是测试用例，又称结构测试、透明盒测试、逻辑驱动测试或基于代码的测试

### 根据测试的粒度

**方法测试**：测试某一个方法的接收和返回数据、运行是否会出现问题     
**单元测试**：理解成一些个方法的测试，又称模块测试，是开发者编写的一小段代码，用于检验被测代码的一个很小的、很明确的**功能**是否正确      
**集成测试**：有关联的几个不同模块，一起测试。是单元测试的逻辑扩展。它的最简单的形式是：两个已经测试过的单元组合成一个组件，并且测试它们之间的接口

### 根据测试的次数

**冒烟测试**：smoke test，不停点击，点点点，虐虐虐。android monkey 可以来帮助我们执行，可以设置执行哪个应用程序，执行多少次等     
使用方法：adb shell monkey -p \<程序的包名> -v  \<事件的数量>         
**压力测试**：Android用的不多，Web网站用的多，看极限承受能力

- - -

## Android下的Junit单元测试

### 在工程内部测试

进行一个Junit测试的具体步骤如下（一个工程内部的测试）：

* 一般是在一个Android工程下，除了MainActivity所在的包，还有：一个或多个**功能包**，里面是一些功能类；单独弄一个测试包，里面专门存放测试类
* 测试类是继承AndroidTestCase，来具体测试功能包中对应的一些方法/功能
* 加了测试类，就需要在manifest文件中：

{% highlight xml %}
    <instrumentation  //指令集
        android:name="android.test.InstrumentationTestRunner" //固定的，指定测试信息
        android:targetPackage="com.gavin.phonedial" />  //指定要测试的目标程序的包名，就是主包名
    <application
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name" >
        <uses-library android:name="android.test.runner" />  //固定的，指定要引用的测试包(使用的函数库)
    </application>
 {% endhighlight %}

* 最后测试类中写好各个测试方法，**直接选中方法名**或者在**outline**中选中其中的一个测试方法，右键run as Andrid Junit test，就可以针对targetPackage中的某个方法/功能进行测试
 
 下面是一个测试类中的一个测试方法，假定我们测试的是自己写的一个Int加法

 {% highlight java %} 
public class Test extends AndroidTestCase {

	public void testAdd() {
		int result = MyMathUtils.addInt(5, 6);
		
		// 断言, 断定某一个对象就是某一个值
		//若是不相等就抛出异常
		assertEquals(11, result);
	}
}
{% endhighlight %}

###  Android Test Project

之前的Junit测试是在一个工程内部写的测试类来测试的，我们可以在外部的工程来测试，这就是在新建的时候选择Android Test Project，新建测试工程的时候就会选择一个你要测的那个工程，之后的操作只需编写一个继承自AndroidTestCase测试类，然后写测试方法就行，无需配置manifest文件

- - -

## Android中的log

一般的java工程运行的话，直接就可以在控制台输出并显示一些运行的日志情况，因为是java的虚拟机环境下；    
android因为虚拟机环境不同，实际运行在一个实际的设备，运行的日志没办法直接在windows的控制台输出。这些日志在手机内部的缓冲区，我们可以通过logcat来取出这些日志。通过adb logcat来获取。

log信息的等级：

* **verbose**：提醒  黑色
* **debug**：调试  蓝色
* **info**：信息  绿色    与system.out同颜色
* **warn**：警告  橙色  与systrm.err同颜色
* **error**：错误  红色

在某处打印log可以这样写：    
**Log.v(tag,msg);**        
v代表verbose；tag是输出后的log的标签，自己设置，一般就是类名，方便辨识；msg则是输出的log具体信息，自己视情况而定。      
一般打印log信息写在具体的方法/功能中，不在测试类中写（直接进行测试），方便进行识别。

- - -

## Junit测试配合debug

我们可以debug as android application，也可以编写好Junit测试之后，**选中对应的方法**debug as Android Junit Test。一些常用操作如下：

* resume是从前一个断点运行到下一个断点
* 进入step into(F5)之后若发现进了系统类，不是自己想调试的，就可以step return跳出
* 选中一串代码，可以watch，看一下在此处的运行结果
* 类加载器初始会加载静态方法，若是调试时静态方法被直接跳过，而你想要调试此静态方法，可以在静态方法里面设置一个断点，然后resume即可进入，进去后再单步调试