---
title: cSharp开启线程的四种方式
date: 2023-03-10 20:23:00
tags: 
		- C# 
		- 多线程
categories: 
		- C#
		- 多线程
cover: https://pic.imgdb.cn/item/640c2bc1f144a01007d1a2f0.jpg
---
# C#开启线程的四种方式

## 一、异步委托开启线程

```c#
public static void Main(string[] args){
	Action<int,int> a=add;
	a.BeginInvoke(3,4,null,null);//前两个是add方法的参数，后两个可以为空
	Console.WriteLine("main()");
	Console.ReadKey();
}
	
static void add(int a,int b){
	Console.WriteLine(a+b);
}
```

```c#
//输出：
main()
7
```

如果不是开启线程，像平常一样调用的话，应该先输出7，再输出main（）

## 二、通过thread类开启线程

```c#
public static void Main(string[] args){
	Thread t=new Thread(DownLoadFile_My);//创建了线程还未开启
	t.Start("http://abc/def/**.mp4");//用来给函数传递参数，开启线程
	Console.WriteLine("main()");
	Console.ReadKey();
}
		
//thread开启线程要求：该方法参数只能有一个，且是object类型
static void DownLoadFile_My(object filePath){
	Console.WriteLine("开始下载："+filePath);
	Thread.Sleep(2000);
	Console.WriteLine("下载完成！");
}
```

```c#
//输出
main()
下载完成！
```

## 三、通过线程池开启线程

```c#
public static void Main(string[] args){
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	ThreadPool.QueueUserWorkItem(DownLoadFile_My);
	Console.WriteLine("main()");
	Console.ReadKey();
}
static void DownLoadFile_My(object state){
	Console.WriteLine("开始下载...    线程ID："+Thread.CurrentThread.ManagedThreadId);
	Thread.Sleep(2000);
	Console.WriteLine("下载完成！");
}
```

```c#
//输出
下载开始... 线程ID:4
下载开始... 线程ID:3
main()
下载开始... 线程ID:5
下载开始... 线程ID:6
下载完成!
下载开始... 线程ID:4
下载完成!
下载开始... 线程ID:3
下载开始... 线程ID:7
下载完成!
下载开始... 线程ID:5
下载开始... 线程ID:8
下载完成!
下载完成!
下载完成!
下载完成!
下载完成!
下载完成!
```

## 四、通过任务开启线程

### 1、Task开启线程

```c#
using System;
using System.Threading;
using System.Threading.Tasks;
public static void Main(string[] args){
	Task t=new Task(DownLoadFile_My);
	t.Start();
	Console.WriteLine("main()");
	Console.ReadKey();
}
static void DownLoadFile_My( ){
	Console.WriteLine("开始下载...    线程ID："+Thread.CurrentThread.ManagedThreadId);
	Thread.Sleep(2000);
	Console.WriteLine("下载完成！");
}
```

```c#
//输出
main()
开始下载... 线程ID：3
下载完成！
```

### 2、Task Factory开启线程

```c#
public static void Main(string[] args){
	TaskFactory tf=new TaskFactory();
	tf.StartNew(DownLoadFile_My);
	Console.WriteLine("main()");
	Console.ReadKey();
}
static void DownLoadFile_My( ){
	Console.WriteLine("开始下载...    线程ID："+Thread.CurrentThread.ManagedThreadId);
	Thread.Sleep(2000);
	Console.WriteLine("下载完成！");
}
```

```c#
//输出
main()
开始下载... 线程ID：3
下载完成！
```

