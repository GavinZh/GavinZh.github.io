---
layout: post
title: Android布局及点击事件
description: Android五大布局、布局文件的叙述，以及四种点击事件的总结
date: 2015-02-13
category: Android
---

## 常见的单位 
布局文件中的单位主要有几种：px，dp和sp

* **px：**就是具体的像素点，这样的话不同分辨率的手机就会相对不一样，VGA指的就是显示器480px \* 640px
* **dp：**==dip， device independent pixels设备独立像素。这个和设备硬件， 推荐使用这个，不依赖像素，随屏幕分辨率大小来变大小，保证相对大小不变。具体是通过像素密度比来计算的：320 \* 480的屏是一个基数。若屏幕是480的宽，像素密度比就是480/320=1.5，若你设置的160dp，就会自动换算成160dp \* 1.5=240px，还是占用屏幕的一半
* **sp：**针对文字，与dp类似，可以根据用户系统的字体自适应，文字缩放时不产生锯齿    
**Tip：**为了适应不同分辨率的屏幕，控件都用dp，字来用sp

- - -

## 几种常见布局
布局就是规定了Android界面的整体排布，由**res/layout**下的xml文件来描述。具体有五种布局，分别是：相对布局、线性布局、。我们在新建的时候选择Android XML Layout File，然后选择一个主布局（root element）即可。先看一下在布局文件中常用到的一些单位，然后对具体的五种布局分别阐述。

### 线性布局--LinerLayout
最主要的三个参数就是：

{% highlight java %} 
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:orientation="horizontal" 
{% endhighlight %}

**width**和**height**就是宽度和高度的属性，"match_parent"和"fill_parent"都是一样的填充父窗体，"wrap_content"是根据内容自调节。**orientation**指定了线性布局的排列规则是水平(horizontal)还是竖直(vertical)。     

针对在线性布局中的**控件**，除了按照orientation定义的排列方向排列，还有一些自己的**内部规则**：

* **layout_gravity：**当前**控件在父元素中**的位置。比如说：定义了一个orientation为vertical，放两个button(宽wrap_content高match_parent)，默认这俩按钮都是在父元素的左侧垂直排列。此时若在第一个button添加一个属性**android:layout_gravity="right"**，就会排到右边
* **gravity：**控制当前**控件内容**显示区域。还是上面的例子，若是一个button叫"茄子"，其宽match_parent，则默认"茄子显示在中间"，若是添加一行**android:gravity="right|center_vertical"**,字就会**显示在button的右侧**，且在垂直方向居中。    
**Tip：**为了调整位置android:layout_gravity和android:gravity都可以多重定位，比如**right|center_vertical**，表示靠右且竖直居中。     
**注意：**父元素定义了vertical，则子控件都是垂直排，这时候，**android:layout_gravity="top"**就没有意义，因为其竖直位置已定，不存在上下问题。        
* **weight：**权重分配，实际上是**剩余空间的分配**。比如说orientation为horizontal，水平的三个button，右边肯定有部分**剩余空间**。若是给第三个button加一个属性**android:layout_weight="1"**，另两个button默认就是0，效果就是把剩下的区域全给第三个button。(此处1也可是2,3,4...)若前两个button都是weight="1"，则说明将剩余空间分成两份，均分给俩button。    
**Tip：**也可以先在线性布局中加入参数Layout_weightSum="N"，然后将这N份分给下面的几个控件。这个属性默认可以省略的~      
* **visibility：**是否显示。有三个属性：visible，invisible，gone。invisible虽然不显示，但是占用空间；gone不显示且空间也不占。

### 相对布局--RelativeLayout
通过一系列的手动定位来实现布局，主题思想就是相对布局中的一个控件是根据**与另一个的相对位置**来排列的

**android:layout_toRightOf**	在指定控件的右边      
**android:layout_toLeftOf**		在指定控件的左边      
**android:layout_above**		在指定控件的上边      
**android:layout_below**		在指定控件的下边      
**android:layout_alignBaseline**	跟指定控件水平对齐，按照中心线      
**android:layout_alignLeft**		跟指定控件左对齐，注意跟**toLeftOf**的区别      
**android:layout_alignRight**	跟指定控件右对齐      
**android:layout_alignTop**		跟指定控件顶部对齐      
**android:layout_alignBottom**	跟指定控件底部对齐      
**android:layout_alignParentLeft**	是否跟父布局左对齐      
**android:layout_alignParentTop**	是否跟父布局顶部对齐      
**android:layout_alignParentRight**	是否跟父布局右对齐      
**android:layout_alignParentBottom**	是否跟父布局底部对齐      
**android:layout_centerVertical**		在父布局中垂直居中      
**android:layout_centerHorizontal**	在父布局中水平居中      
**android:layout_centerInParent**	在父布局中完全居中      

不用死记，知道有：跟指定控件及父窗体的对齐方式，跟指定部件的相对位置即可。

### 绝对布局--AbsoluteLayout 
用的比较少，就是用x和y的坐标（**layout_x**和**layout_y**）来指定控件的绝对位置。    
**应用场景：**一般是游戏里面，比如说子弹的发射，就是x坐标不断的改变。

### 帧布局--FrameLayout 
帧布局每次添加的控件都显示在最上面，最后显示在界面上的是最后添加的一个控件     
**应用场景：**在播放器中，暂停时需要在屏幕中央有个继续按钮，点完后继续播放，按钮消失。     
**注意：**帧布局中的控件也可以**layout_gravity**属性，当值为**center**就是说在父窗体的**正中央**，若是在线性布局中**center**达不到相似的效果，原因就是：若线性布局中若按照vertical排，默认都是靠左，**center**只能实现靠左垂直居中；若是horizontal也是类似。

### 表格布局--TableLayout 

用得比较少，表格布局中每一行都是一个TableRow，TableRow单元行里的单元格的宽度小于默认的宽度时就不起作用，其默认是fill_parent(所以这个宽可以不设定)，高度可以自定义大小。

**布局**（就是规定整体，每个TableRow的列都在内）的几个参数：

{% highlight java %} 
//以下都是指定具体要操作哪一列(从0开始)
android:shrinkColumns		收缩列
android:stretchColumns		拉伸列
android:collapseColumns		隐藏列
{% endhighlight %}

具体每个**列内部**（就是具体某个TableRow中的列）的几个参数：

{% highlight java %} 
android:layout_column	指定显示到哪一列（比如原先是在第二列，先把他弄到第一列，就设为1）
android:layout_span		合并列，从当前开始向后合并，2的话就是合并后面一个
{% endhighlight %}

- - -

## 编写布局文件一些注意事项

* 一些控件的text不要直接用汉字等字符串，先在**string.xml**文件中声明**<string name="menu_settings">Settings</string>**， 然后再通过** @string/Settings**使用，目的是在有**中文**时国际化的时候方便，直接操作一个string.xml文件即可
* EditText可以设置输入类型，比如说电话号码就可以限制为只能输入数字

- - -

## 按钮的几种点击事件

### onclick+点击方法
首先在Button中定义一个onclick属性**android:onClick="startCall"**    
然后在需要的activity中定义一个**public void startCall (View view) {}**方法，方法主体就是点击按钮时触发的事件。

### 获取button+setOnClickListener
先获取到button，然后button.setOnClickListener，由于setOnClickListener需要的参数是一个OnClickListener接口，所以此处有两种处理方法：

* 采用匿名内部类的方法，实现OnClickListener接口。具体参考代码如下：

{% highlight java %} 
Button button = (Button) findViewById(R.id.bt_start);
button.setOnClickListener(new View.OnClickListener() {		
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		System.out.println("要触发的内容，可以另外单独定一个private方法，此处来调用就行");
	}
});
{% endhighlight %}

* 也可以单独在外面新建一个内部类实现OnClickListener接口，然后**button.setOnClickListener(new MyListener());**即可
* 还可以直接activity直接实现OnClickListener接口，然后**button.setOnClickListener(this);**即可

- - -

## 总结
上面的几种方法用的最多的就是activity直接实现OnClickListener接口，然后内部类的两种方式也用的较多。第一种onclick的方法用的较少，原因就是这样做会使文件关联性较差，不符合程序设计的**"解耦思想"**，你必须查看xml文件才能知道这边这个**public void startCall()**方法的具体用途。

