---
layout: post
title: 可以取消的任务
description: 可以取消的任务
tags: [iOS]
---
##应用场景
程序中耗费较长时间的操作通常会被封装成任务(如请求网络数据，文件压缩)，任务执行过程中要有必要的回调(比如当前的进度)，任务结束后也要通知用户。如果任务由于外部原因一直阻塞，此时提供结束任务的接口非常重要。
##任务对象
```obj

typedef void (^TaskProgressBlock)(NSString *title, CGFloat progress, NSDictionary *params);
typedef void (^TaskCompletionBlock)(BOOL finished, NSError *error);

@interface TaskOperation : NSObject
@property(nonatomic,copy) TaskProgressBlock progressBlock;

- (void)cancel;

@end

```