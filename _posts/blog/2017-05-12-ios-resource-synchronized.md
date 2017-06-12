---
layout: post
title: 多线程中的资源同步
description: 从多语言多角度和分析在多线程的环境中如何让共享资源保持同步
category: blog
tag: ios,synchronized,NSThread,NSLock
---

## 关于资源同步

### 资源同步的概念

所谓的资源同步就是在多个线程中，共享同一资源，由于每个线程都对这一公共的资源都有操作，所以有可能回发生：A线程正在利用资源r做操作时，而资源r却在此时被线程B所修改了。这就有可能会引起一系列的问题。所以资源同步就是指：当线程A利用资源r的时候，不允许其它线程访问资源r，这就是多线程中的资源同步。

* 在单线程中，资源一定是同步的
* 只有多线程中的共享资源(公共资源)才需要资源同步

### 资源同步发生的场景

在平常的编码中，当我们声明某一个变量时，我们首先要想到这个变量的使用场景：即是不是在多线程环境下，该变量(资源)是不是在不同的线程中都被操作(包括读取和修改),如果存在，那么就需要对这个资源做线程同步。

## 资源同步的经典场景(买票)

用多线程来模拟一个买票的程序，如果不加资源同步的话，买票的结果就是乱七八糟的，100张票有可能被卖了150次。在这里：

* 票的总张数是一定的
* 一个线程代表一个买票窗口

下面我们用不同语言来做资源同步操作。

### OC中的资源同步

OC中的资源同步有多种方式，下面我具体罗列出来：

* 用@synchronized 关键字
* 使用NSLock 加锁的方式(锁一定要成对出现，不然的话就会发生死锁)

下面是具体的代码示例 [示例程序](https://github.com/MaxwellQi/Thread/tree/master/synchronized/TestSynchronized)：

	#import "ViewController.h"
	
	@interface ViewController ()
	@property (nonatomic,assign) int ticketsCounts;  // 票总张数
	@property (nonatomic,strong) NSThread *sellThread1;  // 卖票线程1
	@property (nonatomic,strong) NSThread *sellThread2; // 卖票线程2
	
	@property (nonatomic,strong) NSLock *ticketsLock;
	@end
	
	@implementation ViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    // Do any additional setup after loading the view, typically from a nib.
	    self.ticketsCounts = 10;
	    self.sellThread1 = [[NSThread alloc] initWithTarget:self selector:@selector(sellWindow01) object:nil];
	    self.sellThread2 = [[NSThread alloc] initWithTarget:self selector:@selector(sellWindow02) object:nil];
	    [self.sellThread1 start];
	    [self.sellThread2 start];
	    
	    
	    self.ticketsLock = [[NSLock alloc] init];
	}
	
	- (void)sellWindow01
	{
	
	/** 同步代码块  **/
	//    @synchronized (self) {
	//        
	//        while (self.ticketsCounts) {
	//            
	//            if (self.ticketsCounts == 0) {
	//                [self.sellThread1 cancel];
	//                NSLog(@"window01: 票卖完了... ");
	//                return;
	//            }
	//            self.ticketsCounts -= 1;
	//            NSLog(@"window01 卖了一张票,剩余票数：%d ",self.ticketsCounts);
	//            usleep(1000*1000); // 休眠1s
	//            
	//        }
	//        
	//    }
	    
	    
	/** 加锁 **/
	    [self.ticketsLock lock];
	        while (self.ticketsCounts) {
	
	            if (self.ticketsCounts == 0) {
	                [self.sellThread1 cancel];
	                NSLog(@"window01: 票卖完了... ");
	                return;
	            }
	            self.ticketsCounts -= 1;
	            NSLog(@"window01 卖了一张票,剩余票数：%d ",self.ticketsCounts);
	            usleep(1000*1000); // 休眠1s
	            
	        }
	    [self.ticketsLock unlock];
	    
	
	}
	
	- (void)sellWindow02
	{
	/** 同步代码块  **/
	//    @synchronized (self) {
	//        
	//        while (self.ticketsCounts) {
	//            
	//            if (self.ticketsCounts == 0) {
	//                [self.sellThread2 cancel];
	//                NSLog(@"window02: 票卖完了... ");
	//                return;
	//            }
	//            self.ticketsCounts -= 1;
	//            NSLog(@"window02 卖了一张票,剩余票数：%d ",self.ticketsCounts);
	//            usleep(1000*1000); // 休眠1s
	//            
	//        }
	//
	//        
	//    }
	    
	
	 /** 加锁 **/
	        [self.ticketsLock lock];
	        while (self.ticketsCounts) {
	
	            if (self.ticketsCounts == 0) {
	                [self.sellThread2 cancel];
	                NSLog(@"window02: 票卖完了... ");
	                return;
	            }
	            self.ticketsCounts -= 1;
	            NSLog(@"window02 卖了一张票,剩余票数：%d ",self.ticketsCounts);
	            usleep(1000*1000); // 休眠1s
	            
	        }
	        [self.ticketsLock unlock];
	}
	
	/*
	  触摸屏幕停止卖票
	 */
	- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
	{
	    [self.sellThread1 cancel];
	    [self.sellThread2 cancel];
	    NSLog(@"停止买票，现在剩余票数=%d",self.ticketsCounts);
	}
	
	- (void)didReceiveMemoryWarning {
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	
	@end
 
 利用宏定义，来简化加锁操作并带有日志：
 
	 #define TVULOCK(weblock)\
	do{\
	    [weblock lock];\
	    NSLog(@"%s----lock-----%d",__func__,__LINE__);\
	}while(0)
	
	#define TVUUNLOCK(weblock)\
	do{\
	    [weblock unlock];\
	    NSLog(@"%s----unlock-----%d",__func__,__LINE__);\
	}while(0)