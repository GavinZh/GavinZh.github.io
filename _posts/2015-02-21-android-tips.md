---
layout: post
title: Android一些知识点
description: 零碎的知识点，需要时直接在这里找
date: 2015-02-21
category: Android
---

## Activity相关

有时候不希望一个activity的顶部显示应用程序名称，处理方法如下：

* onCreate方法中，在setContentView之前，加入**requestWindowFeature(Window.FEATURE_NO_TITLE);**
* xml文件中，布局根结点或者每个控件，都可以通过background来设置背景

- - -

## 其他

某个子线程休眠几秒再执行某动作，这个休眠可以有几种处理方式

* **Thread.sleep();** 这个会有异常，需要try catch
* **SystemClock.sleep();** 这个不会抛异常