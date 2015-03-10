---
layout: post
title: 权限以及存储方式
description: Android中的权限以及存储方式，包括内外部存储
date: 2015-02-22
category: Android
---

## 在手机的内部存储中的存取

手机内部存储的具体存储位置，有两种方法：

* 我们可以自己手动来写，比如：**/data/data/包名/文件夹/文件名**
* 可以通过应用程序的上下文来获取，**context.getFilesDir()**就是获取了**/data/data/包名/files**，还有**getcachedir()**获取了**/data/data/包名/cache**目录

### 写入

要写入内部存储，利用一个输出流来将要存的数据写入文件即可      
下面是一个存取账号密码的小例子，设置成静态为了方便调用

{% highlight java %} 
public static boolean saveUserInfo(String username,String passwd,Context context)
	{
		//context上下文帮我们方便的得到应用程序的环境：资源路径，保存路径，文件路径
		boolean booindex=true;
		File file=new File(context.getFilesDir(),"textinfo.txt");
		FileOutputStream fileOutputStream=null;

		try {
			fileOutputStream=new FileOutputStream(file);
			fileOutputStream.write((username+"  "+passwd).getBytes());	
		} catch (Exception e) {
			e.printStackTrace();
			booindex=false;
		}finally{
			try {
				fileOutputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return booindex;
	}
{% endhighlight %}

### 读取

紧接着上面的存储文件，我们读取存储的文件中的数据，要点：

* 用到了BufferedReader及其方法readline，BufferedReader可以读取string，提高了读取效率
* string的split方法可以根据自己设定的分隔符来分隔一个字符串

代码如下：

{% highlight java %} 
public static Map<String, String> getSavedInfo(Context context)
{
	File file=new File(context.getFilesDir(),"textinfo.txt");
	FileInputStream fileInputStream=null;
	BufferedReader bufferedReader=null;
	Map<String, String> map1=new HashMap<String,String>();
	try {
		fileInputStream=new FileInputStream(file);
		//InputStreamReader将字节流转化为字符流，然后再使用BufferedReader为其增加缓冲功能,
		bufferedReader=new BufferedReader(new InputStreamReader(fileInputStream));
		//readLine()方法会在读取到使用者的换行字符时，再一次将整行字符串传入
		String string1=bufferedReader.readLine();
		String infos[]=string1.split("  ");  //根据两个空格来分隔，分别存入数组

		map1.put("username", infos[0]);
		map1.put("passwd", infos[1]);
	} catch (Exception e) {
		e.printStackTrace();
	}
	finally
	{
		try {
			bufferedReader.close();
			fileInputStream.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	return map1;
}
{% endhighlight %}

- - -

## 外部存储SD卡中的存取

存取SD卡就需要权限了，而且需要事先判断SD卡的挂载状况。

### 写入SD卡

写入和上述是一样的原理，利用一个输出流来将要存的数据写入文件即可。不同的是，写入sd卡需要一个权限，而且在写入前需要检测SD卡状态。从SD卡读的话不需要权限，不过现在可以在开发者选项里选择读也受到保护。下面是写入SD卡的小例子    

{% highlight java %} 
public static boolean saveUserInfo(String username,String passwd,Context context)
	{
		
		boolean booindex=true;
		File file=new File(Environment.getExternalStorageDirectory(), "iiinfo.txt");
		FileOutputStream fileOutputStream=null;

		try {
			//getExternalStorageState来检测SD卡状态，是否挂载了
			if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
				fileOutputStream=new FileOutputStream(file);
				fileOutputStream.write((username+"  "+passwd).getBytes());					
			}else {
				booindex=false;
				Toast.makeText(context, "SD卡不存在，请检查SD卡", 0).show();
			}
		} catch (Exception e) {
			e.printStackTrace();
			booindex=false;
		}finally{
			try {
				fileOutputStream.close();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		return booindex;
	}
{% endhighlight %}

### 从SD卡读

与从手机内存读是一样的逻辑，不同的就是**Environment.getExternalStorageDirectory()**来获取文件位置，读取之前需要判断SD卡是否正确挂载。

- - -

## 获取内存的状态

主要是借鉴安卓源码的操作方法，注意安卓源码不同于sdk，源码是指系统的各个应用程序的源代码实现，sdk则是开发工具包。我们参照系统应用里获取sd卡和系统内存剩余空间和已用空间的方法，来获取内存的状态。

导入系统源码时，由于不是一个个完整的安卓工程，所以需要先新建一个**Android Project From Existing Code**，然后选择需要导入的源码即可，出现报错直接忽略，因为我们缺省一些隐藏库。

要点：    

* 主要是根据存储介质的路径来获取一个磁盘状态对象StatFs，根据此对象可以获得扇区的一些参数
* Formatter.formatFileSize方法，可以将一个long类型的数转换为以bytes, kilobytes, megabytes等为单位的String

{% highlight java %} 
// path可以是sd卡或是手机内存的路径
private String getMemInfo(File path) {
	// 获得磁盘状态对象，主要看path
	StatFs statFs = new StatFs(path.getPath());
	long blockSize = statFs.getBlockSize(); // 单个扇区大小
	long blockCount = statFs.getBlockCount(); // 扇区总数
	long availableBlocks = statFs.getAvailableBlocks(); // 剩余扇区数

	String totalSize = Formatter.formatFileSize(this, blockCount
			* blockSize); // 总空间
	String spareSize = Formatter.formatFileSize(this, availableBlocks
			* blockSize); // 总空间

	String memStateString = "总空间：" + totalSize + "\n" + "剩余空间：" + spareSize;
	return memStateString;
}
{% endhighlight %}

若是要获取sd卡的状态，只需**File sdPath = Environment.getExternalStorageDirectory();**        
若是要获取手机内存的状态，只需**File dataPath = Environment.getDataDirectory();**       

- - - 

## 权限问题

Android下的权限跟linux是完全一致：    
它的三段代表的含义：

* 当前应用程序权限  
* 当前应用程序所在的组权限  
* 其他用户权限

一般每个独立的应用程序是一个独立的组，所以呢前两段就是一样的。比如说：      
-rw- rw- --- 按二进制来111就是7=4+2+1


### 写数据

以下是写数据时带权限的方法：

fileOutputStream=context.openFileOutput(文件名,mode);       
fileOutputStream.write((username + "  " + passwd).getBytes());        
先是创建不同属性的输出流，之后写入就行了（目录是**/data/data/包名/文件夹/文件名**）；若文件不存在自动创建

**关于mode**：        
0就是默认的,亦即MODE_PRIVATE,此外，MODE_WORLD_READABLE,MODE_WORLD_WRITEABLE,MODE_WORLD_WRITEABLE+MODE_WORLD_READABLE都可以，理解成来控制**文件本身的权限**，就是写完之后此文件的权限。

而写数据的一般的情况则是：    
先创立一个File：new File(File 路径,String 名字)，然后new FileOutputStream(file),最后写入：fileOutputStream.write(内容)；前两步可以合并成一步：new FileOutputStream(File 路径)。默认写完后文件都是MODE_PRIVATE的，即外部程序不可读不可写。

### 读数据

所谓的可读可写等权限的搭配都是针对外部的其他程序的（上述的第三段：-rw- rw- ---），本程序都是可读可写。    
默认写完后文件都是MODE_PRIVATE的，即外部程序不可读不可写。

- - - 

## 使用SharedPreferences来存取

创建一个SharedPreferences实例时，与context.openFileOutput类似，不过此处的目录是**/data/data/包名/shared_prefs/save1.txt.xml**，无论如何都会在文件末尾加个xml。这种方法比较便捷，存入的时候采用<键值，数值>的形式，则取得时候很方便，**无需使用输入输出流**。

### 写数据

{% highlight java %}
//得到一个SharedPreferences实例，注意此处有权限
SharedPreferences sharedPreferences=context.getSharedPreferences("save1.txt", Context.MODE_PRIVATE);
//得到一个编辑对象
Editor editor=sharedPreferences.edit();
//此时只是存入内存
editor.putString("username", username); //两个参数：key，string值
editor.putString("passwd", passwd);
//提交，真正存数据
editor.commit();
{% endhighlight %}

### 读数据

与写数据不同的是，读数据时无需获取一个编辑对象，因为不需要对文件进行操作了，直接getString即可。

{% highlight java %}
//得到一个SharedPreferences实例，注意此处有权限
SharedPreferences sharedPreferences=context.getSharedPreferences("save1.txt", Context.MODE_PRIVATE);
//通过key值来读取数据  
String username=sharedPreferences.getString("username", null);
String passwd=sharedPreferences.getString("passwd", null);
if (!TextUtils.isEmpty(username) && !TextUtils.isEmpty(passwd)) 
{
	Map<String, String> userMap=new HashMap<>();
	userMap.put("username", username);
	userMap.put("passwd", passwd);
	return userMap;
}else {
	return null;
}
{% endhighlight %}