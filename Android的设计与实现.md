#第一部分 基础篇

* 第一章 Android体系结构及源代码阅读环境搭建
* 第二章 框架基础JNI



# 第二部分启动篇


# 第三部分 Binder篇


# 第四部分 消息通信篇
* 第七章 线程消息同学与异步处理


# 第五部分 Package Manager篇

# 第六部分 Activity Manager篇
* 第十章 Activity Manager的机制与实现

	ActivityManager是Android框架层提供的核心模块之一。activity管理器只是它的众多功能之一
	
	* 1 ActivityManager概述
		
		它提供以下的几种功能
		- 启动或者杀死应用程序进程
		- 启动并调度Activity生命周期
		- 启动并调度应用程序service生命周期
		- 注册Broadcase Receiver ，并接收和分发Broadcast
		- 启动并发布Content Provider
		- 调度task
		- 检查、授予、收回访问URI的权限
		- 处理应用程序crash
		- 调整进度调度优先级及策略（调整OOM adj）
		- 查询当前系统运行状态（包括memory、Graphic、cup、Database等）
		
		它主要由以下六部分组成
		
		- Binder接口：由IBinder和Binder提供进程间通信的接口。
		- 服务接口：IInterface 和IActivityManager提供系统服务的接口，此处的IActivityManager不是通过.AIDL文件转化而来的。
		- 服务中枢：ActivityManagerNative继承自Binder并实现了IActivityManager，它提供服务接口和Binder接口的相互转化功能，并在内部存储服务代理对象并提供getDefault方法返回服务代理。
		- 服务代理：由ActivityManagerProxy实现，用于与server端提供的系统服务进行进程间通讯。
		- Client：由ActivityManager封装一部分服务接口供Client调用。ActivityManager内部通过调用ActivityManagerNative的getDefault方法，可以得到一个ActivityManagerProxy对象的引用，进而通过该代理对象调用远程服务的方法。
		- Service：由ActivityManagerService实现，提供server端的系统服务。
		
	* 2 ActivityManagerService在系统启动阶段的主要工作
	
		ActivityManagerService是在系统启动的init2阶段，由SystemServer启动的Java系统服务之一。
			
			相关的代码
			class ServerThread extends Thread{
			public void run(){
				Looper.prepare();准备消息循环
				Context context = null;
				try{
				//第一阶段 ：调用main方法启动ActivityManagerService
				context = ActivityManagerService.main(factoryTest);
				...
				//第二阶段 ：调用setSystemProcess方法
				ActivityManagerService.setSystemProcess();
				...
				//第三阶段 ：调用installSystemProvider方法
				ActivityManagerService.installSystemProvider();
				....
				//关联windowsManagerService
				ActivityManagerService。self().setWindowManager（wm）;
				}catch(RuntimeException e){...}
				...
				try{
				//
				
				第四阶段 调用systemReady方法
				ActivityManagerService.self().systemReady(new Runnable()
				{public void run()
				{
				}
				)}
				Looper.loop();
				}
				
	* 3 第一阶段：启动ActivityManagerService
	
			、、代码部分
			public static fianl Context main(int factoryTest){
			//1启动AThread线程
			AThread thr = new AThread();
			
	
	
	 	主要由四步组成
		- 启动AThread线程，该线程负责创建ActivityManagerService。如果ActivityManagerservice未创建完毕，则调用main方法的ServiceThread线程需要等待AThead线程的通知。
		- ActivityManagerService创建完毕后，调用ActivityThread的SystemMain方法创建ActivityThread对象，并赋值到ActivityManagerService的静态成员变量mSystemThread中.
		- 调用ActivityThread的getSystemContext方法创建Context对象，并赋值到ActivityManagerService的成员变量mContext，然后对其他成员变量（ActivityStack）赋值，结束后，通知AThread线程。
		- 调用ActivityManagerService的StartRunning方法。				
		######3.1 启动AThread线程 		
		ActivityManagerService启动过程的第一步：启动AThread线程 
		
			static class AThread extends Thread {
			ActivityManagerService mService;//保存
			....
			
			
		  由上面代码可以看到，虽然在ServerThread线程中调用了ActivityManagerService的Main方法，但是ActivityManagerService的构造过程却是放在新的线程AThread中完成，为了保证这两个线程状态的一致性，在main方法和AThread线程的run方法中，分别调用了wait和notifyall方法实现线程的相互等待。其中main方法等待的是AThread成功创建，并初始化ActivityManagerservice对象；AThread等待的是main方法对ActivityManagerService对象的后续处理完成。
		  
		  然后是ActivityManagerService的创建和初始化在其构造函数中完成。代码如下
		  
		  	代码
		  	...
		  	
		  由上面代码看出，ActivityManagerService的构造并不复杂，主要工作如下
		  
		  - 初始化BroadcastQueue
		  
		  - 初始化一些与系统运行状态相关的变量，如耗电，CPU使用率，负载等信息
		  
		  - 获取系统配置信息，如OpenGL版本，字体，语言，屏幕等信息
		  
		  - 将自身加入到Android Watchdog监控中。
		  
		  ActivityManagerService穿件昌岗后，将首先存入到AThread.mService成员变量中，并通知main方法所在线程该对象创建成功，main方法所在线程会根据它是否为空，来决定是否进入第二次等待状态。。相互的判定。进入各自的状态。
		  		
		  
		######3.2 创建ActivityThread对象	
				
				
			
			
		
		
		
	



