####导出头文件

使用前面文章介绍的Clutch([4.3 Clutch：iOS应用破解工具](http://security.ios-wiki.com/issue-4-3/))破解IPA，然后把IPA拷贝到Mac上。

然后使用Mac上安装的class-dump-z([使用class-dump-z获得iOS应用程序的类信息](http://security.ios-wiki.com/issue-3-5/))就可以导出头文件。具体使用方法请参阅上述两篇文章。

####分析头文件
这里我们的需求和地理位置有关系，我们首先搜索下关于Location的文件。

我们找到这个文件：MomoLocationManager.h。

其内容如下：

    /**
     * This header is generated by class-dump-z 0.2a.
     * class-dump-z is Copyright (C) 2009 by KennyTM~, licensed under GPLv3.
     *
     * Source: (null)
     */
    
    #import <XXUnknownSuperclass.h> // Unknown library
    #import "CLLocationManagerDelegate.h"
    
    @class CLLocation, CLLocationManager, NSDate, NSTimer;
    
    __attribute__((visibility("hidden")))
    @interface MomoLocationManager : XXUnknownSuperclass <CLLocationManagerDelegate> {
    	CLLocationManager* locManager;
    	CLLocation* location;
    	CLLocation* reviseLocation;
    	CLLocation* fakeLocation;
    	BOOL correctLocation;
    	NSTimer* timer;
    	BOOL isLocationing;
    	NSDate* beginDate;
    	NSDate* lastLocTime;
    }
    @property(retain, nonatomic) CLLocation* fakeLocation;
    @property(retain, nonatomic) CLLocation* reviseLocation;
    @property(retain, nonatomic) CLLocation* location;
    @property(copy, nonatomic) NSDate* lastLocTime;
    @property(retain, nonatomic) CLLocationManager* locManager;
    @property(retain, nonatomic) NSDate* beginDate;
    +(id)shareMomoLocationManager;
    -(id)distanceBetweenLocationDictionary:(id)dictionary;
    -(BOOL)isOriginLocationValid;
    -(BOOL)isReviseLocationValid;
    -(void)locationManager:(id)manager didFailWithError:(id)error;
    -(void)locationManager:(id)manager didUpdateToLocation:(id)location fromLocation:(id)location3;
    -(void)refreshLocationIfExceedLimit;
    -(void)HandleTimer;
    -(void)updateServerLocation;
    -(void)locationFail;
    -(void)locationFinish;
    -(void)updateSelfLocation:(id)location;
    -(void)cancelLocation;
    -(void)reviseLocationToError:(id)error;
    -(void)reviseLocationToFail:(id)fail;
    -(void)reviseLocationToSuccess:(id)success;
    -(void)reviseLocationTo;
    -(void)stoplocation;
    -(void)locationTimeOut;
    -(void)starLocationAndCorrectLocation:(BOOL)location;
    -(id)getLatestLocationWithInterval:(double)interval;
    -(void)dealloc;
    -(id)init;
    @end


<br>
这个文件很有意思，很可能就是我们要找的。

那怎么确定这个类确实是我们想要的呢？

我们可以对这个文件的所有方法挂钩(编写Tweak)，先打印下调用记录，并分析其参数值，最终确定是不是这个类。

编写Tweak的方法参见：[Theos：iOS越狱程序开发框架](http://security.ios-wiki.com/issue-3-6/)

我们知道，如果要挂钩某个方法，类似如下代码：

    #import <SpringBoard/SpringBoard.h>
    
    %hook SpringBoard
    
    -(void)applicationDidFinishLaunching:(id)application {
    %orig;
    
    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Welcome" 
    message:@"Welcome to your iOS Device Ted!" 
    delegate:nil 
    cancelButtonTitle:@"security.ios-wiki.com" otherButtonTitles:nil];
    
    [alert show];
    [alert release];
    
    }
    
    %end


如果我们要对一个类的所有方法，包括property的挂钩（Hook)，手动一个个写当然可以，但是那样就太繁琐了。下一节我们介绍一个工具，可以一下就对整个类的所有方法挂钩。

请继续阅读下一节。


***
[#8 修改某陌生人交友软件的位置信息下的更多文章](http://security.ios-wiki.com/issue-8/)