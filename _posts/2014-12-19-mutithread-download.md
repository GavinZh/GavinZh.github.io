---
layout: post
title: Java中的多线程下载
description: 简述了Java中的多线程下载以及断点下载机制
category: blog
---

## 预备知识
**Http请求数据**&ensp;**多线程**

## 多线程下载的原理
我们先从一个图来直观地看一下多线程下载的原理：
![MutiThread](/images/other/mutithread-illustradate.jpg)
我们可以理解为：在下载的时候把服务器上的资源划分为几块，每块由一个线程负责下载。

整体的思路如下：

* 既然要把资源划分为几块，所以我们先获得资源的大小--用到了HttpURLConnection类中的getContentLength()方法
* 本地创建一个空白文件，大小等于**要下载的文件大小**。要用到一个类RandomAccessFile--用于操作本地的文件。
* 事先定义一个内部类继承自Thread，run()方法中主要指定了每个线程从服务器的获取对应的数据，写入本地文件对应的位置中；
* 开启线程进行下载

## 多线程下载的实现
为了之后检测文件下载的完整性，我选择下载了exe文件（使用Tomcat来实现搭建一个本地服务器）。下面是整体的代码实现，为了书写简单直观，没有加入异常捕获。

###获取资源大小并在本地创建空白文件
代码中已经标注了关键的实现字段。主要是getContentLength()方法和RandomAccessFile类的使用。关于RandomAccessFile，[这边文章][1]讲得不错。

```ruby
	private final int threadCount = 3;  //要开启的线程数
	private long blockSize;  //要下载的每个区块的大小

	URL url = new URL("http://localhost:8080/YoudaoDict.exe");
	connection = (HttpURLConnection) url.openConnection();
	connection.setRequestMethod("GET");
	connection.setConnectTimeout(10000);
	connection.setReadTimeout(5000);
	connection.connect();
	int responseCode = connection.getResponseCode();
	if (responseCode == 200) {
		int length = connection.getContentLength();    // 获得要下载的资源的大小
		blockSize = length / threadCount; 
		File file = new File("youdao.exe");    //本地文件
		RandomAccessFile raf = new RandomAccessFile(file, "rw");   //raf来操作本地文件,rw是可读可写
		raf.setLength(length);    //设定这个本地文件的大小，为将要下载资源的大小
		}
```

###创建一个类继承自Thread
为了获取主函数中的一些数据，此处定义DownloadThread为内部类。初始化需要四个参数：下载源、线程编号、开始下载的位置、结束下载的位置。

```ruby
	private static class DownloadThread extends Thread {

		private int threadID;
		private long startIndex;
		private long endIndex;
		private String path;

		public DownloadThread(int threadID, long startIndex, long endIndex,
				String path) {
			super();
			this.threadID = threadID;
			this.startIndex = startIndex;
			this.endIndex = endIndex;
			this.path = path;
		}

		@Override
		public void run() {

			HttpURLConnection connection = null;
			InputStream is = null;
			RandomAccessFile raf = null;
			try {
				URL url = new URL(path);
				connection = (HttpURLConnection) url.openConnection();
				connection.setRequestMethod("GET");
				// setRequestProperty告诉服务器要下载的起始位置
				connection.setRequestProperty("Range", "bytes=" + startIndex
						+ "-" + endIndex);
				connection.setReadTimeout(5000);
				connection.setConnectTimeout(10000);
				connection.connect();

				int responseCode = connection.getResponseCode();
				if (responseCode == 206) {     // 下载某一段文件的返回码是206
					// 1. 先获得流
					is = connection.getInputStream();
					// 2. 再获得文件及写入文件的位置--还是之前的那个空白文件
					File file = new File("youdao.exe");
					raf = new RandomAccessFile(file, "rw");
					 // seek指定在空白文件处开始写的位置
					raf.seek(startIndex);
					// 3. 最后就可以将流写入文件
					int len = 0;
					byte[] buffer = new byte[1024];
					while ((len = is.read(buffer)) != -1) { 
						raf.write(buffer, 0, len);
					}
				}
			}
			......catch和finally省略，一定要记得在finally中关闭HttpURLConnection、InputStream、RandomAccessFile。
```

###开启多个线程下载
```ruby
	for (int i = 1; i <= threadCount; i++) {
		long startIndex = (i - 1) * blockSize;
		long endIndex = i * blockSize - 1;
		if (i == threadCount) {
			endIndex = length - 1;
			}
		String path = "http://localhost:8080/YoudaoDict.exe";
		new DownloadThread(i, startIndex, endIndex, path).start();
		}
```

## 多线程断点下载的原理
断点下载原理：

* 有几个线程下载，就保存几个文件：文件中记录各个线程之前下载的总量；
* 每次开始下载时，每个线程都先检查上次是否有下载，然后及时更新每个线程下载的开始位置，再开始下载；
* 最后所有线程都下载完毕，才可以把记录文件都删除，否则某个线程下完就删可能会造成这部分重新下载。

## 多线程断点下载的实现
基于以上思想，我们需要一个临时文件来存储每个线程的下载量，为下次下载提供依据，这些我们放在线程DownloadThread里面执行，首先要在调用setRequestProperty告诉服务器下载位置之前，及时更新下载位置，代码实现如下：

```ruby
	int total = 0;  //记录下载总量
	File positionFile = new File(threadID + ".txt");

	// 接着从上一次的位置继续下载数据
	if (positionFile.exists() && positionFile.length() > 0) {   // 判断是否有记录
		FileInputStream fis = new FileInputStream(positionFile);
		BufferedReader br = new BufferedReader(new InputStreamReader(fis));
		// 获取当前线程上次下载的总大小是多少
		String lasttotalstr = br.readLine(); // readLine只是读一行，所以就是直接读出来了
		int lastTotal = Integer.valueOf(lasttotalstr);
		startIndex += lastTotal;  //根据文件记录更新下载的起始位置
		total += lastTotal;   // 更新下载总量
		fis.close();
	}
```
然后在每个线程往file("youdao.exe")写入文件之时，要记得根据下载量来及时更新positionFile中的记录文件，一定要注意写在while循环中，否则没法在每个buffer写入file("youdao.exe")时进行及时更新，代码实现如下：

```ruby
// 正常返回数组中的数据长度，读完就会返回-1
	while ((len = is.read(buffer)) != -1) {
		FileOutputStream fos = new FileOutputStream(positionFile);
		rcf.write(buffer, 0, len);
		total += len;   //根据当前下载及时更新文件记录的下载总量
		//写在while循环中就是要不断及时更新下载量到本地记录文件
		fos.write(String.valueOf(total).getBytes());
	}
```

## 总结
Java的多线程下载整体思路十分清晰



[1]: http://blog.csdn.net/akon_vm/article/details/7429245  "Java RandomAccessFile用法"
[2]: http://www.taobao.com  "跳往淘宝"