---
layout: post
title: 可以取消的任务
description: 可以取消的任务
tags: [iOS]
---
##应用场景
程序中耗费较长时间的操作通常会被封装成任务(如请求网络数据，文件压缩)，任务执行过程中要有必要的回调(比如当前的进度)，任务结束后也要通知用户。如果任务由于外部原因一直阻塞，此时提供结束任务的接口非常重要。
##任务对象
```objc

/**
 *  任务进度
 *
 *  @param title    描述
 *  @param progress 完成度
 *  @param params   参数
 */
typedef void (^TaskProgressBlock)(NSString *title, CGFloat progress, NSDictionary *params);

/**
 *  任务完成
 *
 *  @param finished 是否完成,如果任务被取消或者异常则为NO
 *  @param error    错误
 */
typedef void (^TaskCompletionBlock)(BOOL finished, NSError *error);

@interface TaskOperation : NSObject
@property(nonatomic,copy) TaskProgressBlock progressBlock;

/**
 *  结束任务
 */
- (void)cancel;

@end

```

##任务管理

```objc

/**
 *  任务管理
 */
@interface TaskManager : NSObject
/**
 *  执行任务
 *
 *  @param completion 任务完成时执行
 *
 *  @return 任务对象
 */
- (TaskOperation *)executeWithCompletion:(TaskCompletionBlock)completion;
@end

```

##使用

```objc

TaskOperation *operation = [taskManager executeWithCompletion:^(BOOL finished, NSError *error) {
        NSLog(@"finished");
}];
    
operation.progressBlock = ^(NSString *title, CGFloat progress, NSDictionary *params){
        NSLog(@"%@",title);
        //[operation cancel];
};

```

##实现
- TaskOperationExecutor.h

```objc
#import "TaskOperation.h"

@class TaskOperationExecutor;
/**
 *  任务执行
 *
 *  @param executor 任务对象
 */
typedef void (^TaskBlock)(TaskOperationExecutor *executor);

/**
 *  取消任务
 */
typedef void (^TaskCancelBlock)();

/**
 *  任务的实现
 */
@interface TaskOperationExecutor : TaskOperation
@property(nonatomic,copy) TaskCompletionBlock completion;
@property(nonatomic,assign) volatile BOOL running;
@property(nonatomic,retain) NSError *error;

/**
 *  主进程中回调进度
 *
 *  @param title    标题
 *  @param progress 进度
 *  @param params   参数
 */
- (void)invokeProgressBlock:(NSString *)title progress:(CGFloat)progress params:(NSDictionary *)params;

/**
 *  执行任务
 *
 *  @param block  执行任务块
 *  @param cancel 取消任务块
 */
- (void)executeTask:(TaskBlock)block withCancel:(TaskCancelBlock)cancel;

/**
 *  执行异步操作
 *
 *  @param cancel 取消任务块
 */
- (void)beginTaskWithCancel:(TaskCancelBlock)cancel;

/**
 *  完成任务
 */
- (void)endTask;

@end

```

- TaskOperationExecutor.m
```objc

#import "TaskOperationExecutor.h"

@interface TaskOperationExecutor ()
@property(nonatomic,copy) TaskBlock block;
@property(nonatomic,copy) TaskCancelBlock cancelBlock;

- (void)invokeCompletionBlock;
- (void)invokeCancelBlock;

@end

@implementation TaskOperationExecutor

- (void)dealloc
{
    self.cancelBlock = nil;
    self.block = nil;
    self.completion = nil;
    self.progressBlock = nil;

    [super dealloc];
}

- (void)invokeCompletionBlock
{
    dispatch_async(dispatch_get_main_queue(), ^{
        @autoreleasepool {
            if (self.completion)
            {
                self.completion(self.running,self.error);
                
                self.cancelBlock = nil;
                self.block = nil;
                self.completion = nil;
                self.progressBlock = nil;
            }
        }
    });
}

- (void)invokeCancelBlock
{
    dispatch_async(dispatch_get_main_queue(), ^{
        @autoreleasepool {
            if (self.cancelBlock)
            {
                [self cancelBlock];
            }
        }
    });
}

- (void)invokeProgressBlock:(NSString *)title progress:(CGFloat)progress params:(NSDictionary *)params
{
    dispatch_async(dispatch_get_main_queue(), ^{
        @autoreleasepool {
            if (self.progressBlock)
            {
                self.progressBlock(title,progress,params);
            }
        }
    });
}

- (void)executeTask:(TaskBlock)block withCancel:(TaskCancelBlock)cancel
{
    self.running = YES;
    self.block = block;
    self.cancelBlock = cancel;
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        @autoreleasepool {
            if (self.block)
            {
                self.block(self);
            }
            [self invokeCompletionBlock];
        }
    });
}

- (void)beginTaskWithCancel:(TaskCancelBlock)cancel
{
    self.running = YES;
    self.cancelBlock = cancel;
}

- (void)endTask
{
    [self invokeCompletionBlock];
}

- (void)cancel
{
    self.running = NO;
    
    [self invokeCancelBlock];
    [self invokeCompletionBlock];
}

@end

```