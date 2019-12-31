title: 使用NSOperation和NSOperationQueue启动iOS多线程
date: 2011-05-08 23:15:02
tags:
	- ios
	- multi thread
categories:
	- programming
	- objectivec
---


在app store中的很多应用程序非常的笨重，他们有好的界面，但操作性很差，比如说当程序从网上或本地载入数据的时候，界面被冻结了，用户只能等程序完全载入数据之后才能进行操作。
当打开一个应用程序时，iphone会产生一个包含main方法的线程，所用程序中的界面都是运行在这个线程之中的（table views, tab bars, alerts…）,有时候我们会用数据填充这些view，现在问        题是如何有效的载入数据，并且用户还能自如的操作程序。方法是启动新的线程，专门用于数据的下载，而主线程不会因为下载数据被阻塞。
不管使用任何编程语言，在实现多线程时都是一件很麻烦的事情。更糟糕的是，一旦出错，这种错误通常相当糟糕。然而，幸运的是apple从os x10.5在这方面做了很多的改进，NSThread的引入，使得开发多线程应用程序容易多了。除此之外，它们还引入了两个全新的类，NSOperation和NSOperationQueue。
接下来我们通过一个实例来剖析如何使用这两个类实现多线程。这里指示展示这两个类的基本用法，当然这不是使用他们的唯一办法。

<!--more-->

如果你熟悉java或者它的别的变种语言的话 ，你会发现NSOperation对象很像java.lang.Runnable接口，就像java.lang.Runnable接口那样，NSOperation类也被设计为可扩展的，而且只有一个需要重写的方法。它就是－(void)main。使用NSOperation的最简单的方式就是把一个NSOperation对象加入到NSOperationQueue队列中，一旦这个对象被加入到队列，队列就开始处理这个对象，直到这个对象的所有操作完成。然后它被队列释放。
下面的例子中，使用一个获取网页，并对其解析程NSXMLDocument，最后将解析得到的NSXMLDocument返回给主线程。
    
``` [objc] PageLoadOperation.h
@interface PageLoadOperation : NSOperation {
    NSURL *targetURL;
}
@property(retain) NSURL *targetURL;
- (id)initWithURL:(NSURL*)url;
@end
```

``` [objc] PageLoadOperation.m
#import "PageLoadOperation.h"
#import "AppDelegate.h"
@implementation PageLoadOperation
@synthesize targetURL;
- (id)initWithURL:(NSURL*)url;{
    if (![super init]) 
        return nil;
    [self setTargetURL:url];
    return self;
}
- (void)dealloc {
    [targetURL release];
    targetURL = nil;
    [super dealloc];
}
- (void)main 
{
    NSString *webpageString = [[[NSString alloc]
    initWithContentsOfURL:[self targetURL]] autorelease];
    NSError *error = nil;
    NSXMLDocument *document = [[NSXMLDocument alloc]
    initWithXMLString:webpageString 
    options:NSXMLDocumentTidyHTML error:&error];
    if (!document) {
        NSLog(@"%s Error loading document (%@): %@", _cmd, [[self targetURL] absoluteString], error);
         return;
    }
    [[AppDelegate shared]
    performSelectorOnMainThread:@selector(pageLoaded:) withObject:document waitUntilDone:YES];
    [document release];
}
@end
```

正如我们所看到的那样，这个类相当的简单，在它的init方法中接受一个url并保存起来，当main函数被调用的时候，它使用这个保存的url创建一个字符串，并将这个字符串传递给NSXMLDocumentinit方法。如果加载的xml数据没有出错，数据会被传递给AppDelegate，它处于主线程中。到此，这个线程的任务就完成了。在主线程中注销操作队列的时候，会将这个NSOperation对象释放。

``` [objc] AppDelegate.h
@interface AppDelegate : NSObject {
    NSOperationQueue *queue;
}
+ (id)shared;- (void)pageLoaded:(NSXMLDocument*)document;
@end
```

``` [objc] AppDelegate.m        
#import "AppDelegate.h"
#import "PageLoadOperation.h"
@implementation AppDelegate
static AppDelegate *shared;
static NSArray *urlArray;
- (id)init
{
    if (shared)
    {
        [self autorelease];
        return shared;
    }
    if (![super init]) 
        return nil;    
    NSMutableArray *array = [[NSMutableArray alloc] init];
    [array addObject:@"http://www.google.com"];
    [array addObject:@"http://www.apple.com"];
    [array addObject:@"http://www.yahoo.com"];
    [array addObject:@"http://www.zarrastudios.com"];
    [array addObject:@"http://www.macosxhints.com"];
    urlArray = array;    
    queue = [[NSOperationQueue alloc] init];
    shared = self;
    return self;
    }
 
-(void)applicationDidFinishLaunching: (NSNotification *)aNotification
{
        for (NSString *urlString in urlArray) 
        {
        NSURL *url = 
        [NSURL URLWithString:urlString];        
        PageLoadOperation *plo = [[PageLoadOperation alloc] initWithURL:url];
        [queue addOperation:plo];
        [plo release];
        }
}
- (void)dealloc
{
        [queue release], queue = nil;
        [super dealloc];
}
+ (id)shared;
{
        if (!shared) {
            [[AppDelegate alloc] init];
        }
        return shared;
}
- (void)pageLoaded:(NSXMLDocument*)document;
{
        NSLog(@"%s Do something with the XMLDocument: %@", _cmd, document);
}
@end
```

NSOperationQueue的并行控制（NSOperationQueue Concurrency）
        在上面这个简单的例子中，我们很难看出这些操作是并行运行的，然而，如果你你的操作花费的时间远远比这里的要长，你将会发现，队列是同时执行这些操作的。幸运的是，如果你想要为队列限制同时只能运行几个操作，你可以使用NSOperationQueue的[setMaxConcurrentOperationCount:]方法。例如，[queue setMaxConcurrentOperationCount:2];