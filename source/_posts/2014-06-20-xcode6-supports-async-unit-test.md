title: XCode6 中XCTest支持异步的单元测试
tags:
    - unit test
categories:
    - programming
    - objectivec
---

过去因为每个测试是相互独立、顺序执行的，因此单元测试都是同步执行的。为了支持各种异步调用，XCode可以用来新的API和 <span style="color: #666666;">XCTestExpectation </span>对象支持block，现在测试方法会等待调用完成或者超时。
一般的过程是，测试用例进行异步操作，并注册回调，然后等待expectation被满足。当异步操作结束时，通常会进入回调函数或者Block，测试案例在这里需要主动告知expectation已经被满足，从而结束run loop的等待，然后可以进行后续断言或操作。

<!--more-->

[objc]

// Test that the document is opened. Because opening is asynchronous,
// use XCTestCase's asynchronous APIs to wait until the document has
// finished opening.
// 测试某文档应当被打开。使用异步API来等待，直到文档的打开动作完成。

- (void)testDocumentOpening
{
 // Create an expectation object.
 // This test only has one, but it's possible to wait on multiple expectations.
 // 创建XCTestExpectation 对象。可以等待多个expectation。
 XCTestExpectation *documentOpenExpectation = [self   expectationWithDescription:@&quot;document open&quot;];

 NSURL *URL = [[NSBundle bundleForClass:[self class]]
 URLForResource:@&quot;TestDocument&quot; withExtension:@&quot;mydoc&quot;];
 UIDocument *doc = [[UIDocument alloc] initWithFileURL:URL];

 [doc openWithCompletionHandler:^(BOOL success) {
     XCTAssert(success);
     // Possibly assert other things here about the document after it has opened...
     // 文档打开之后，还可以有其他的断言

     // Fulfill the expectation-this will cause -waitForExpectation
     // to invoke its completion handler and then return.
     // 满足期望-这将导致后面的 -waitForExpectation 调用其completion handler然后返回。
     [documentOpenExpectation fulfill];
 }];

 // The test will pause here, running the run loop, until the timeout is hit
 // or all expectations are fulfilled.
 // 测试会等在这里，运行着run loop，直到超时或者expectation被满足
 [self waitForExpectationsWithTimeout:1 handler:^(NSError *error) {
     [doc closeWithCompletionHandler:nil];
 }];
}

[/objc]

&nbsp;

&nbsp;

&nbsp;

<span style="color: #042eee;"><span style="text-decoration: underline;">https://developer.apple.com/library/prerelease/ios/documentation/DeveloperTools/Conceptual/testing_with_xcode/testing_3_writing_test_classes/testing_3_writing_test_classes.html</span></span>