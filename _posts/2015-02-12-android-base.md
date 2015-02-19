---
layout: post
title: Android一些基础知识
description: 包括Dalvik VM，安卓环境的配置，目录结构等基础知识
category: blog
---

## Dalvik VM
Dalvik VM是google为android设计的虚拟机（类比于java虚拟机JVM），google设计它的原因是：避免和Sun公司JVM版权问题；针对移动设备。

Dalvik VM和JVM的比较，首先来看**JVM**：

* JVM是基于堆栈的架构
* 编译后的文件格式：.java->.class(通过javac.exe)->.jar(多个class文件打包)   class文件和jar包都可以被JAVA虚拟机解释执行
* 一个jar文件中包含有多个class文件，每个class文件都有其完整的结构，比较占用资源

再来看**Dalvik VM**：

* Dalvik VM是基于寄存器的架构，因为是CPU直接访问自己的寄存器进行操作，所以速度要快于基于堆栈的
* 编译后的文件格式：.java->.class->.dex(通过dex.bat)->.apk。其中.dex是android中的字节码文件，可在DVM中解释执行
* 一个apk文件只有一个dex文件，节省资源

## 在MyEclipse中的环境搭建
个人用的是MyEclipse来操作的，所以介绍一下在MyEclipse下android开发环境的搭建
### 几个概念
**sdk**：standard develop kit，android的标准开发包  
**avd**：android virtual device  
**adt**：android develop tools，eclipse及MyEclipse的插件，更方便在MyEclipse中创建管理android工程  
### 具体安装配置过程

* 先单独下载sdk包：[sdk下载地址][1]。然后在系统环境变量的Path中添加tools文件夹的路径，比如我的就是`D:\Program Files\android-sdk\tools;`
* 用sdk manager来更新sdk，注意要是下载不成功，可以在tools中下面force成http，然后在host文件中加developer.android.com,dl.google.com,dl-ssl.google.com的ip，这样一般可以下载成功
* MyEclipse中在线下载安装adt，help->install new software，填写 https://dl-ssl.google.com/android/eclipse/ ，完后next就行，一路全选
* 在MyEclipse中，window->preferences->android那一项，填写sdk的路径，比如我的是`D:\Program Files\android-sdk`
* 注意Android_SDK_HOME是用来保存安卓虚拟机的位置的目录，并不是指sdk的目录，在创建AVD之前要在系统环境变量中自己配置一下Android_SDK_HOME，然后模拟器相关的信息就会在Android_SDK_HOME\.android目录下

## AVD及SDK补充
### sdk manager中的一些解释
**extra目录下：**
![sdk manager](/images/blog/sdk-manager-2.PNG)

* android support library是实现版本的向下兼容
* google almod ad sdk 加小广告
* extra中还有些有关推送和用户分析的一些扩展。

每个版本都有对应的**API level**，在对应目录下面有：
![sdk manager](/images/blog/sdk-manager-1.PNG)

* sdk platform(用到的一些jar包)
* 一些sdk示例程序
* 对应不同平台虚拟机的镜像
* google api：提供了谷歌一些支持的服务的jar包，比如通过这个可以使用谷歌的一些服务，可以直接使用google地图等
* sdk的全部源代码（source for android sdk）

**tools**节点下
![sdk manager](/images/blog/sdk-manager-3.PNG)
sdk tools，sdk platform-tools，build-tools在下面均会讲到

### sdk目录下一些常用项
sdk目录如下图，这个与上述sdk manager中的下载列表中的内容都是相对应的，二者结合理解
![sdk目录](/images/blog/sdk-file.PNG)

* 在sdk\platform-tools下（关于连接管理调试设备）：adb.exe（android debug bridge），是在ddmm中左边那一栏显示连接的一些设备，若是设备显示不出来，可以在下拉栏选择reset adb   
* 在sdk\build-tools目录（关于编译的）：dx.bat：它就是把.class的文件打包做成.dex文件   
* 在sdk\platform目录（关于开发工具包）：存放不同android平台的一些开发工具包
* sdk\samples下：不同平台的一些实例    
* sdk\tools（关于模拟器）：emulator.exe是来运行实现模拟器，draw9path.bat是处理图形的一个工具，ddms.bat，Dalvik Debug Monitor Service，是 Android 开发环境中的Dalvik虚拟机调试监控服务     
* sdk\sources（关于源码）：存放源码，一系列的.java文件。sdk实际上提供一些jar包，这些jar包对应的源代码都在这里   
* sdk\extras\intel\Hardware_Accelerated_Execution_Manager：这里面可以安装硬件加速器，之后启动以Intel为CPU的模拟器会快很多   
* sdk\system-images（关于模拟器的镜像）：与sdk manager下载时选择的项目对应，创建AVD时可选    
* sdk\add-on（临时目录）：sdk下载过程中的临时目录，下载完就自动清空


### 创建AVD时注意项

在创建安卓虚拟机时，device definition中的一些常见分辨率：

* VGA最早指的是显示器480*640这种显示模式
* QVGA：240*320（quarter 1/2*1/2）
* HVGA：320*480（640的half）
* WVGA：480*800（wide，变800了）
* FWVGA：480*854    
选择的时候选大的会占用较多的内存，选个小的就行了，HVGA或WVGA

另外一些参数：

* 选CPU/ABI时因为在电脑上开发，所以选Intel的会快一些，虽然大多数安卓机是ARM的
* internal storage 200M足够，sd卡32m足够，因为开发不会占用太大
* 模拟器左上角的5554是代表端口号，就是手机现在的电话号码

**创建模拟器时候的一些小问题：**     

首先一定保证sdk跟Android_SDK_HOME是全英文路径，然后常见问题如下     

**问题一：**android模拟器无法保存数据      
每创建一个模拟器，都会在每个模拟器的对应目录下生成四个文件夹，用来标记这四个当前模拟器已经开启，见下图。正常退出模拟器，这四个文件夹会删除。若电脑意外重启或者模拟器非正常关闭，这几个文件夹不会被删除，系统任务中模拟器还在运行，就会导致新开的模拟器无法保存数据。    
**解决方法**：手动删除这四个lock文件夹并重新开启模拟器
![avd目录](/images/blog/android-avd.PNG)
**问题二：**android模拟器无信号     
**解决方法一：**连上互联网     
**解决方法二：**连上局域网， 然后设置如下：

```ruby
局域网IP，比如:192.168.1.100
掩码，比如：255.255.255.0
网关，一般是：192.168.1.1
DNS服务器，局域网的路由器IP，一般是：192.168.1.1
```
**解决方法三：**什么网都没连， 然后设置如下：

```ruby
IP:192.168.1.100
掩码：255.255.255.0
网关和DNS：192.168.1.100
```
## Android工程目录结构
android整体的目录结构如下图
![android目录](/images/blog/android-directory.PNG)
在workspace中的目录中与这个工程目录基本相似，需要注意的是源码所在目录下的.settings文件夹，里面有一些配置信息，若是意外关闭，MyEclipse打不开，把这个目录删掉即可  

**下面对目录结构依次说明：**
![android目录](/images/blog/android-directory-2.PNG)
**src：**包含了自己编写的java源文件   

**gen：**自动生成的一些文件，R.java,BuildConfig.java。其中R.java文件存放res目录下资源的ID，自动生成的，方便之后的操作，别改动。Android开发工具会自动根据你放入res目录的资源，同步更新修改R.java文件。R.java在应用中起到了字典的作用，它包含了各种资源的id，通过R.java，应用可以很方便地找到对应资源。另外编绎器也会检查R.java列表中的资源是否被使用到，没有被使用到的资源不会编绎进软件中，这样可以减少应用在手机占用的空间。       

**Android4.4.2：**是当初选创建android工程时选的compile with，这儿的jar包就是开发所用的android sdk了。不过也可以改，右键工程->property->Android，就可以修改project build target。（也可以通过project.properties文件修改）     

**assets：**资产目录，存放程序需要的一些媒体文件，比如图片，数据库等，将来会被打包到应用程序apk中。Android除了提供/res目录存放资源文件外，在/assets目录也可以存放资源文件，而且/assets目录下的资源文件不会在R.java自动生成ID，所以读取/assets目录下的文件必须指定文件的路径，如：file:///android_asset/xxx.3gp            

**bin：**工程的编译目录. 存放一些编译时产生的临时文件和当前工程的.apk文件。project->clean可以清空
![android目录](/images/blog/android-directory-3.PNG)
**lib：**一些当前工程依赖的第三方jar包，这儿加了会被添加到android private libraries目录   

**res：**资源目录

* drawable：存放一些不同分辨率的png、jpg等图标及图片。在代码中使用getResources().getDrawable(resourceId)获取该目录下的资源。
* layout：布局文件，可以定义一个具体界面。
* values：一些字符串及布局的模板样式定义存放在这里，之后要使用相关变量或样式直接用"名字"来呼唤即可。strings.xml 存放android字符串.dimens.xml 存放屏幕适配所用到的尺寸.style.xml 存放android下显示的样式.

例如： `android:text="@string/hello_world"`  @代表R.java文件 ,string代表R文件的string内部类，然后就找到了叫"hello_world"这个string。而这个string具体的定义地方在/values/strings.xml。在Activity中使用`getResources().getString(resourceId)`或`getResources().getText(resourceId)`取得资源         

* anim 存放定义动画的XML文件。
* xml 在Activity中使用`getResources().getXML()`读取该目录下的XML资源文件。
* raw 该目录用于存放应用使用到的原始文件，如音效文件等。编译软件时，这些数据不会被编译，它们被直接加入到程序安装包里。 为了在程序中使用这些资源，你可以调用`getResources().openRawResource(ID)` , 参数ID形式：`R.raw.somefilename`。
* menu: 存放android的OptionsMenu菜单的布局.
* values-sw600dp：7寸平板所对应的值，string.xml
* values-v14：指定4.0版本以上的手机显示的样式，style.xml

**AndroidManifest.xml：**当前应用程序的清单文件，包括：程序的配置信息，启动图标，应用程序名称，是否在桌面显示图标(有一段代码)，包名，版本号，最小最大版本号等，android的四大组件都需要在application节点进行相应配置。如果应用使用到了系统内置的应用(如电话服务、互联网服务、短信服务、GPS服务等等)，你还需在该文件中声明使用权限。在此处修改了包名，会相应修改到gen处的包名，但是src处的包名不变，所以需要在src的java文件中引入新的R文件出处。**另外：**应用程序只有签名和包名完全一致才是同一个应用程序~     
**Tip：**应用程序开启时候显示的第一个activity，在其intent-filter下包含如下参数配置   
`/action android:name="android.intent.action.MAIN"/`     
`/category android:name="android.intent.category.LAUNCHER"/`     

**project.properties: **项目环境信息，一般无需修改。指定当前工程采用的开发工具包的版本（即compile with）

**proguard-project.txt: **加密当前程序所使用.

## 一些常用命令 -- adb&emulator
实际上，在MyEclipse中DDMS就是用到了adb来显示连接的设备，并对设备进行监控调试    

我们可以自己配置一下系统环境变量，Path里加一个`D:\Program Files\android-sdk\platform-tools`，然后在cmd中就可以使用adb命令，添加Path里加一个`D:\Program Files\android-sdk\tools`，就可以使用emulator命令，常见命令如下：    
![adb使用](/images/blog/adb-use.PNG)

* `adb devices`：就可以查看所有连接的设备，若是adb挂掉了，也可以用这个命令来顺便启动adb进程
* `adb logcat`：打出log日志，若是adb挂了，也可以顺便启动adb进程
* `adb shell`：挂载到linux空间，直接命令操作查看Android系统(也是一个Linux系统)，多个模拟器时：`adb -s emulator-5554 shell`来启动
* `adb install XXX.apk`：安装应用程序
* `adb uninstall 应用程序包名`：卸载应用程序，注意必须得是包名，在清单文件中查找    
**注：**两个应用程序相同就必须得签名和包名都相同，即使包名相同，签名不同的情况下，没法完成覆盖安装，也就保护了应用程序不被非法更新覆盖      
* `adb pull <remote> <local>`：从模拟器或设备取东西
* `adb push <local> <remote>`：往模拟器或设备存东西，比如：`adb push C:\hello.apk /mnt/sdcard/a.apk`
* `adb kill-server`和 `adb start-server`：关闭和开启adb进程    
**注：**在MyEclipse中点击reset adb就是执行了 `adb kill-server`和`adb start-server`两个指令   
* `emulator -avd AVD名字`：开启模拟器，名字就是avd Manager中设置的名字，如：`emulator -avd first-phone`
* 另外在cmd中：`netstat –ano`可以查看占用端口情况，因为有时其他程序占用了端口会导致调试桥打不开

其实在MyEclipse中的图形化的界面都是调用上述的一些工具来实现，必要的时候到cmd中来执行，效率比较高。

## Android程序打包安装过程
打包安装过程，选中工程->Run as Android Application，这时可以看控制台的log，对应于下面的步骤  :
![apk安装](/images/blog/apk-install.png)   
**首先**会生成apk文件，具体的过程如下：   

* 生成.dex文件
* 首先把res下的xml资源文件从文本格式转化为二进制文件，然后生成资源索引表resources.arsc(会包括res下的所有资源)，然后把resources.arsc转化为二进制文件
* 准备未编译的一些文件，主要包括asset目录下的，以及res下的一些图片文件
* 把清单文件AndroidMenifest.xml转换成二进制，至此，所有apk的所有文件组成已全部就绪
* 使用debug.keystore（位于.android目录下，之前avd设置的那个目录，每台电脑都独一无二）对整个应用程序进行打包签名，apk解压后META-INF里面就有程序的签名信息

**然后**加载apk文件到模拟器中，具体就是通过adb push把apk文件加载到`/data/local/tmp/xxx.apk`

**最后**就会安装应用程序，过程如下(实际先push一下，再加上执行下面的过程，就是adb install)： 

* 把`/data/local/tmp/xxx.apk`文件, **剪切** 至`/data/app/包名-1.apk`
* 在/data/data/文件夹下以**包名**创建一个文件夹, 用于存储当前程序的数据
* 在/data/system下的packages.xml和packages.list文件中分别添加一条关于应用程序的记录，是根据包名来记录的     
**packages.xml文件中：**

```ruby
<package  name="com.gavin.helloword" codePath="/data/app/com.gavin.helloword-1.apk" nativeLibraryPath="/data/data/com.gavin.helloword/lib" flags="0" ft="14921a759c0" it="14921a75be2" ut="14921a75be2" version="1" userId="10043">
<sigs count="1">
<cert index="4" key="签名：有N个多" />
```
**list文件中：**前面是包名，后面是数据的目录     
`com.gavin.helloword` 10043 1 `/data/data/com.gavin.helloword`  

**卸载就是一个相反的过程**，把上述的一些文件和目录一一删除

如果项目太大，就不会通过Run as android application来进行部署，ant+adb install来，其作用就是跟run as…一样的。通过ant执行编译成.class再到.dex，再打包成apk，再签名；然后通过adb安装的整个部署流程，这样执行效率更高

## 总结
讲述了Android工程的一些基础知识，包括：安卓虚拟机Dalvik VM的介绍，在MyEclipse中搭建Android的编译环境，Android SDK Manager及SDK目录下的各个目录的作用及对应关系，简述了MyEclipse中一个Android工程的目录结构，介绍了adb和emulator工具及其常用命令，最后详述了Android的apk打包安装过程。


[1]: http://developer.android.com/sdk/index.html#Other  "sdk下载"
[2]: http://www.taobao.com  "跳往淘宝"