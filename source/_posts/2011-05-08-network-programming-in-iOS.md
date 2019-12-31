title:  iOS网络编程
date: 2011-05-08 23:26:48
tags:
	- ios
	- network
categories:
	- programming
	- objectivec
---

# 一、检测网络状态
## 添加源文件和framework
    
开发Web等网络应用程序的时候，需要确认网络环境，连接情况等信息。如果没有处理它们，是不会通过Apple的审(我们的)查的。
Apple 的例程 Reachability 中介绍了取得/检测网络状态的方法。要在应用程序程序中使用Reachability，首先要完成如下两部：
    
1. 添加源文件：
在你的程序中使用 Reachability 只须将该例程中的 Reachability.h 和 Reachability.m 拷贝到你的工程中。如下图：
2. 添加framework：
将SystemConfiguration.framework 添加进工程。如下图：
    
<!-- more-->

## 网络状态
    
Reachability.h中定义了三种网络状态：

    typedef enum {
        NotReachable = 0,            //无连接
        ReachableViaWiFi,            //使用3G/GPRS网络
        ReachableViaWWAN            //使用WiFi网络
    } NetworkStatus;
    
因此可以这样检查网络状态：

    Reachability *r = [Reachability reachabilityWithHostName:@“www.apple.com”];
    switch ([r currentReachabilityStatus]) {
            case NotReachable:
                    // 没有网络连接
                    break;
            case ReachableViaWWAN:
                    // 使用3G网络
                    break;
            case ReachableViaWiFi:
                    // 使用WiFi网络
                    break;
    }
    
## 检查当前网络环境
程序启动时，如果想检测可用的网络环境，可以像这样

    // 是否wifi
    + (BOOL) IsEnableWIFI {
        return ([[Reachability reachabilityForLocalWiFi] currentReachabilityStatus] != NotReachable);
    }

    // 是否3G
    + (BOOL) IsEnable3G {
        return ([[Reachability reachabilityForInternetConnection] currentReachabilityStatus] != NotReachable);
    }


    - (void)viewWillAppear:(BOOL)animated {    
    if (([Reachability reachabilityForInternetConnection].currentReachabilityStatus == NotReachable) && 
            ([Reachability reachabilityForLocalWiFi].currentReachabilityStatus == NotReachable)) {
            self.navigationItem.hidesBackButton = YES;
            [self.navigationItem setLeftBarButtonItem:nil animated:NO];
        }
    }

## 链接状态的实时通知
网络连接状态的实时检查，通知在网络应用中也是十分必要的。接续状态发生变化时，需要及时地通知用户：
    
Reachability 1.5版本
``` [objc]
    // MyAppDelegate.h
    #import "Reachability.h"

    @interface MyAppDelegate : NSObject <UIApplicationDelegate> {
        NetworkStatus remoteHostStatus;
    }

    @property NetworkStatus remoteHostStatus;

    @end

    // My.AppDelegate.m
    #import "MyAppDelegate.h"

    @implementation MyAppDelegate
    @synthesize remoteHostStatus;

    // 更新网络状态
    - (void)updateStatus {
        self.remoteHostStatus = [[Reachability sharedReachability] remoteHostStatus];
    }

    // 通知网络状态
    - (void)reachabilityChanged:(NSNotification *)note {
        [self updateStatus];
        if (self.remoteHostStatus == NotReachable) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"AppName", nil)
                         message:NSLocalizedString (@"NotReachable", nil)
                        delegate:nil cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
            [alert release];
        }
    }

    // 程序启动器，启动网络监视
    - (void)applicationDidFinishLaunching:(UIApplication *)application {
    
        // 设置网络检测的站点
        [[Reachability sharedReachability] setHostName:@"www.apple.com"];
        [[Reachability sharedReachability] setNetworkStatusNotificationsEnabled:YES];
        // 设置网络状态变化时的通知函数
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(reachabilityChanged:)
                                                 name:@"kNetworkReachabilityChangedNotification" object:nil];
        [self updateStatus];
    }

    - (void)dealloc {
        // 删除通知对象
        [[NSNotificationCenter defaultCenter] removeObserver:self];
        [window release];
        [super dealloc];
    } 
```
    
Reachability 2.0版本
    
``` [objc]
    // MyAppDelegate.h
    #import "Reachability.h"
    @class Reachability;
    @interface MyAppDelegate : NSObject <UIApplicationDelegate> {
            Reachability  *hostReach;
    }
    @end
    
    // MyAppDelegate.m
    #import "MyAppDelegate.h"
    @implementation MyAppDelegate
    - (void)reachabilityChanged:(NSNotification *)note {
        Reachability* curReach = [note object];
        NSParameterAssert([curReach isKindOfClass: [Reachability class]]);
        NetworkStatus status = [curReach currentReachabilityStatus];
    
        if (status == NotReachable) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"AppName""
                              message:@"NotReachable"
                              delegate:nil
                              cancelButtonTitle:@"YES" otherButtonTitles:nil];
                              [alert show];
                              [alert release];
        }
    }
                              
    - (void)applicationDidFinishLaunching:(UIApplication *)application {
        // ...
                  
        // 监测网络情况
        [[NSNotificationCenter defaultCenter] addObserver:self
                              selector:@selector(reachabilityChanged:)
                              name: kReachabilityChangedNotification
                              object: nil];
        hostReach = [[Reachability reachabilityWithHostName:@"www.google.com"] retain];
        hostReach startNotifer];
        // ...
    }
    @end
```

# 二、使用NSConnection下载数据
    
1.创建NSConnection对象，设置委托对象
``` [objc]    
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:[self urlString]]];
    [NSURLConnection connectionWithRequest:request delegate:self];
```

2. NSURLConnection delegate委托方法

``` [objc]
        - (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response;  
        - (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error;  
        - (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data;  
        - (void)connectionDidFinishLoading:(NSURLConnection *)connection;  
```

3. 实现委托方法
``` [objc]
    - (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response {
        // store data
        [self.receivedData setLength:0];            //通常在这里先清空接受数据的缓存
    }
    
    - (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data {
           /* appends the new data to the received data */
        [self.receivedData appendData:data];        //可能多次收到数据，把新的数据添加在现有数据最后
    }

    - (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error {
        // 错误处理
    }

    - (void)connectionDidFinishLoading:(NSURLConnection *)connection {
        // disconnect
        [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;   
        NSString *returnString = [[NSString alloc] initWithData:self.receivedData encoding:NSUTF8StringEncoding];
        NSLog(returnString);
        [self urlLoaded:[self urlString] data:self.receivedData];
        firstTimeDownloaded = YES;
    }
```

# 三、使用NSXMLParser解析xml文件

1. 设置委托对象，开始解析
``` [objc]
    NSXMLParser *parser = [[NSXMLParser alloc] initWithData:data];   //或者也可以使用initWithContentsOfURL直接下载文件，但是有一个原因不这么做：
    // It's also possible to have NSXMLParser download the data, by passing it a URL, but this is not desirable
    // because it gives less control over the network, particularly in responding to connection errors.
    [parser setDelegate:self];
    [parser parse];
```

2. 常用的委托方法
``` [objc]
    - (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName 
                                namespaceURI:(NSString *)namespaceURI
                                qualifiedName:(NSString *)qName 
                                attributes:(NSDictionary *)attributeDict;
    - (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName 
                                namespaceURI:(NSString *)namespaceURI 
                                qualifiedName:(NSString *)qName;
    - (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string;
    - (void)parser:(NSXMLParser *)parser parseErrorOccurred:(NSError *)parseError;
    static NSString *feedURLString = @"http://www.yifeiyang.net/test/test.xml";
```

3.  应用举例
``` [objc]
    - (void)parseXMLFileAtURL:(NSURL *)URL parseError:(NSError **)error
    {
        NSXMLParser *parser = [[NSXMLParser alloc] initWithContentsOfURL:URL];
        [parser setDelegate:self];
        [parser setShouldProcessNamespaces:NO];
        [parser setShouldReportNamespacePrefixes:NO];
        [parser setShouldResolveExternalEntities:NO];
        [parser parse];
        NSError *parseError = [parser parserError];
        if (parseError && error) {
            *error = parseError;
        }
        [parser release];
    }

    - (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI 
                                        qualifiedName:(NSString*)qName attributes:(NSDictionary *)attributeDict{
        // 元素开始句柄
        if (qName) {
            elementName = qName;
        }
        if ([elementName isEqualToString:@"user"]) {
            // 输出属性值
            NSLog(@"Name is %@ , Age is %@", [attributeDict objectForKey:@"name"], [attributeDict objectForKey:@"age"]);
        }
    }

    - (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI 
                                        qualifiedName:(NSString *)qName
    {
        // 元素终了句柄
        if (qName) {
               elementName = qName;
        }
    }

    - (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string
    {
        // 取得元素的text
    }

    NSError *parseError = nil;
    [self parseXMLFileAtURL:[NSURL URLWithString:feedURLString] parseError:&parseError];
```