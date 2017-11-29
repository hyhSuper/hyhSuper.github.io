---
layout: post
title:  "Mac OS 开发 NSTextField文字不居中问题的解决方法"
date:   2017-11-28
---

今年三月份，进入Coacoa OS 开发 ,现在算来已有半年。Mac 开发最让人烦躁的还是UI问题，如果手写代码，那你的效率将会变得非常低，所以xib在Mac OS开发中还是很重要的。然而，在使用NSTextField过程中你会发现一旦你设置其高度大于24，就会出现文字不居中的问题，本文主要解决的就是文本不居中的问题。

首先，自己定义一个继承NSTextFieldCell的类，命名为QYTextFiledCellCenter。实现代码如下：

```objective-c
#import <Cocoa/Cocoa.h>
@interface QYTextFiledCellCenter : NSTextFieldCell
//编辑状态先设置文本的Y坐标
@property (nonatomic) IBInspectable CGFloat oringeY;
@end
```

上面是.h文件。下面的是.m的文件实现：

```objective-c
#import "QYTextFiledCellCenter.h"
@implementation QYTextFiledCellCenter
- (NSRect)titleRectForBounds:(NSRect)rect{
NSRect titleRect = [super titleRectForBounds:rect];
CGFloat minimHeight = self.cellSize.height;
titleRect.origin.y += (titleRect.size.height - minimHeight)/2;
titleRect.size.height = minimHeight;
return titleRect;
}

-(void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView{
  [super drawWithFrame:cellFrame inView:controlView];
}
-(void)selectWithFrame:(NSRect)rect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)delegate start:(NSInteger)selStart length:(NSInteger)selLength{
  
  CGRect titleRect =  [self titleRectForBounds:rect];
  titleRect.origin.y += self.oringeY;
  [super selectWithFrame:titleRect inView:controlView editor:textObj delegate:delegate    start:selStart length:selLength];
 NSLog(@"titleRect = %@",NSStringFromRect(titleRect));

}
@end
```

解释一下：上面的两个方法中

```objective-c
- (NSRect)titleRectForBounds:(NSRect)rect 
```

是text-type Cell类型所调用的函数，这个函数主要的作用就是调整文本信息被描绘的位置，这里我们设置了居中。

```objective-c
-(void)selectWithFrame:(NSRect)rect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)delegate start:(NSInteger)selStart length:(NSInteger)selLength
```

最后，用图片演示一下如何在xib中使用，选中xib中的一个label打开右边的inspector 栏，选择第三个按钮（identify inspector）,然后再Custom Class 一栏填写QYTextFiledCellCenter 图片如下：

  ![xcode](/Users/allan/Documents/GitPages/hyhSuper.github.io/_site/assets/img/xcode.png)