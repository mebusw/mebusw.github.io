title: ObjectiveC中的单例设计模式
date: 2011-04-24 23:46:57

tags:
	- ios
	- singleton
	- design patterns
categories:
	- programming
	- objectivec
---

注意class method中什么时候用self，什么时候用super！

``` [objc]
#import "PostOfficeProxy.h"  
#import "HTTPPostOffice.h"  
static PostOffice *_sharedPostOffice = nil;  
@implementation PostOfficeProxy  
@synthesize realPostOffice;  
+ (PostOffice*)sharedPostOfficeProxy {  
    NSLog(@"shared");  
    @synchronized([PostOffice class]) {  
        if (nil == _sharedPostOffice) {  
            [[self alloc] init];  
              
        }  
    }  
    return _sharedPostOffice;  
}  
+ (id)allocWithZone:(NSZone *)zone {  
    NSLog(@"alloc zone");  
    @synchronized([PostOffice class]) {  
        if (nil == _sharedPostOffice) {  
            _sharedPostOffice= [super allocWithZone:zone];  
              
              
        }  
    }  
    return _sharedPostOffice;  
}  
- (id)init {  
    NSLog(@"init");  
    self = [super init];  
    if (nil != self) {  
        self.realPostOffice = [[HTTPPostOffice alloc] init];  
    }  
      
    return self;  
      
}  
- (id)copyWithZone:(NSZone *)zone {  
    return self;  
}  
- (id)retain    
{    
    return self;    
}    
- (unsigned)retainCount    
{    
    return UINT_MAX;     
}    
- (void)release    
{    
    //do nothing    
}    
- (id)autorelease    
{    
    return self;    
}    
@end  
```