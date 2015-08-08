---
layout: post
title: "iOS开发中的MVC模式与数据转模型的简单应用"    
categories:
- notes
- iOS   
tag:
- MVC
- iOS
- MJExtension  

---

  

## iOS开发中的MVC模式与数据转模型的简单应用

### 1. MVC介绍

MVC ( Model - View - Controller ) 是软件开发中最典型也最频繁使用的设计模式，而在iOS程序开发中则体现得尤为突出。充分理解iOS开发中的MVC设计思想，有助于我们设计出合理且高效的APP程序。

- Model

	 Model层，即数据模型层，用来封装数据对象，提高数据的安全性与可复用性。

- View

 	 View层，即视图层，用来展示数据，为使用者组织合理可视视图。

- Controller
 
 Controller层，即控制层，用来充当视图Model与View的媒介。具体说来，有四个方面：
 
 - 监听View事件 （ View —>  Controller）
 - 同步View控件（Controller —> View ）
 - 更新Model数据 （Controller —> Model）
 - 推送Model数据（Model —> Controller）

下图中可以看出MVC三者相互作用的关系

![MVC](http://ww2.sinaimg.cn/large/5657ca78gw1eut2ju5b3kg20ea0563yf.gif)

进一步了解，可以查[苹果官方文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)。

### 2. Model

建立数据模型的过程可以分为三步：

- 首先，根据需求分析，将具体业务抽象为业务类。


- 其次，将业务对象抽象为数据类型。


- 最后，将数据类型封装为模型对象。

------

从一个简单实例出发来理解Model层的使用：

![demo](http://ww1.sinaimg.cn/large/5657ca78gw1euuvzmavvwj209c0gmdg2.jpg)

上图为一个添加删除包包的Demo ，点击”+” button 会增加一个包包的图片和名字，点击”-“ button 则会删除一个。

由需求可知，包包是一个业务对象，每个包包有唯一的图片和名字。那么，将其抽象之后可以获得一个简单的字典类型数据：

``` objective-c
NSDictionary *dict = @[@"icon":"danjianbang" , @"name":"单肩包"]；
```

将其封装成一个数据模型，即为：

``` objective-c
//  Bag.h

#import <Foundation/Foundation.h>

@interface Bag : NSObject
/** 图标 */
@property (nonatomic, copy)NSString *icon;
/** 名字 */
@property (nonatomic, copy)NSString *name;

/** 通过一个字典来初始化模型对象 */
- (instancetype)initWithDict:(NSDictionary *)dict;
/** 通过一个字典来创建模型对象 */
+ (instancetype)shopWithDict:(NSDictionary *)dict;

@end

```

``` objective-c
//  Bag.m

#import "Bag.h"

@implementation Shop

- (instancetype)initWithDict:(NSDictionary *)dict{
    if (self = [super init]) {
        self.name = dict[@"name"];
        self.icon = dict[@"icon"];
    }
    return self;
}

+ (instancetype)shopWithDict:(NSDictionary *)dict{
    Shop *shop = [[self alloc] initWithDict:dict];
    return shop;
}
@end

```

在模型中声明了`icon`和`name`属性，并添加了模型的初始化方法，`initWithDict`方法通过传入一个字典来初始化模型，`bagWithDict`方法则调用初始化方法来完成模型对象的创建，至此就将业务抽象并封装为数据模型了。

### 3. View

View用来组织视图的显示，将数据合理呈现在界面上。在这个小例子中，只有一个主视图View、两个Button和多个显示Bag的小控件，每个小控件又包括了Bag的图片和名字。所以，我们可以把它们抽出来作为独立的子控件`BagView`，控制器则只用来实现`add`和`remove`方法，以及给子控件`BagView`传递`Bag`模型数据。

``` objective-c
//BagView.h

#import <UIKit/UIKit.h>

@class Bag;

@interface BagView : UIView
/** 商品模型 */
@property (nonatomic, strong) Bag *bag;

- (instancetype)initWithBag:(Bag *)bag;
+ (instancetype)bagViewWithBag:(Bag *)bag;
+ (instancetype)bagView;
@end

```



在`BagView`的声明文件中，添加了`Bag`模型的引用和三个初始化方法。以类名开头的初始化类方法可以为添加`BagView`提供便捷，所以分别提供了`+ (instancetype)bagViewWithShop:(Bag *)bag`和`+ (instancetype)bagView`方法可以实现带参与不带参初始化。

``` objective-c
//BagView.m

#import "BagView.h"
#import "Bag.h"

@interface BagView()
/** 图片 */
@property (nonatomic, weak) UIImageView *iconView;
/** 名字 */
@property (nonatomic, weak) UILabel *nameLabel;
@end

@implementation BagView

- (instancetype)initWithBag:(Bag *)bag
{
    if (self = [super init]) {
        self.bag = bag;
    }
    return self;
}

+ (instancetype)bagViewWithBag:(Bag *)bag
{
    return [[self alloc] initWithBag:bag];
}

+ (instancetype)bagView
{
    return [[self alloc] init];
}

/** init方法内部会自动调用initWithFrame:方法 */
- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        // 添加一个图片
        UIImageView *iconView = [[UIImageView alloc] init];
        [self addSubview:iconView];
        self.iconView = iconView;

        // 添加一个文字
        UILabel *nameLabel = [[UILabel alloc] init];
        nameLabel.textAlignment = NSTextAlignmentCenter;
        [self addSubview:nameLabel];
        self.nameLabel = nameLabel;
    }
    return self;
}

/**
 * 当前控件的frame发生改变的时候就会调用
 * 这个方法专门用来布局子控件，设置子控件的frame
 */
- (void)layoutSubviews
{
    // 一定要调用super方法
    [super layoutSubviews];

    CGFloat bagW = self.frame.size.width;
    CGFloat bagH = self.frame.size.height;

    self.iconView.frame = CGRectMake(0, 0, bagW, bagW);
    self.nameLabel.frame = CGRectMake(0, bagW, bagW, bagH - bagW);
}

- (void)setBag:(Bag *)bag
{
    _bag = bag;   
    self.iconView.image = [UIImage imageNamed:bag.icon];
    self.nameLabel.text = bag.name;
}

@end
```

当调用`init`方法时，会自动调用`- (instancetype)initWithFrame:(CGRect)frame`方法，所以重写`- (instancetype)initWithFrame:(CGRect)frame`方法来添加`image`和`label`子控件。然后`layoutSubviews`会在`frame`改变时自动调用，写好`frame`布局。最后，通过重写`setBag`方法，将`bag`模型对象拆开，并为`image`和`label`赋值。这样就完成了从视图对象的初始化、尺寸布局以及设置数据的整个过程。

### 4. Controller

控制器`ViewController`中，完成了设置数据源和实现`add`和`remove`点击事件的方法。这里使用一个`bags.plist`来作为数据源，然后用懒加载的方式加载`bags.plist`中存放的数据，并将其”字典数组"转换为"模型数据”。

![bags.plist](http://ww2.sinaimg.cn/large/5657ca78gw1euuvwqdd7ej20wc0ledku.jpg)

最后，通过`BagView *bagView = [BagView bagViewWithBag:self.bags[index]]`创建子控件对象，并传入模型数据。再将`bagView`对象添加到`bagsView`父控件中完成商品的添加。

``` objective-c
//  ViewController.m
//

#import "ViewController.h"
#import "Bag.h"
#import "BagView.h"

@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIButton *addBtn;
@property (weak, nonatomic) IBOutlet UIView *bagsView;
@property (weak, nonatomic) IBOutlet UIButton *removeBtn;

@property (nonatomic, strong) NSMutableArray *bags;
@end

@implementation ViewController

// 懒加载
// 1.第一次用到时再去加载
// 2.只会加载一次
- (NSMutableArray *)bags
{
    if (_bags == nil) {
        // 创建"模型数组"
        _bags = [NSMutableArray array];

        // 获得plist文件的全路径
        NSString *file = [[NSBundle mainBundle] pathForResource:@"bags.plist" ofType:nil];

        // 从plist文件中加载一个数组对象(这个数组中存放的都是NSDictionary对象)
        NSArray *dictArray = [NSArray arrayWithContentsOfFile:file];

        // 将 “字典数组” 转换为 “模型数据”
        for (NSDictionary *dict in dictArray) { // 遍历每一个字典
            // 将 “字典” 转换为 “模型”
            Bag *bag = [Bag bagWithDict:dict];

            // 将 “模型” 添加到 “模型数组中”
            [_bags addObject:bag];
        }
    }
    return _bags;
}

#pragma mark 添加
- (IBAction)add {
    /**************  一些常用的变量 begin **************/
    // 每一行的列数
    NSUInteger colsPerRow = 3;
    // 获得当前商品的索引
    NSUInteger index = self.bagsView.subviews.count;
    // 商品宽度
    CGFloat bagW = 70;
    // 商品高度
    CGFloat bagH = 90;
    /**************  一些常用的变量 end **************/


    /**************  计算X值 begin **************/
    // 求出列号
    NSUInteger col = index % colsPerRow;
    // 每一列之间的间距
    CGFloat xMargin = (self.bagsView.frame.size.width - colsPerRow * bagW) / (colsPerRow - 1);
    // 商品X
    CGFloat bagX = (bagW + xMargin) * col;
    /**************  计算X值 end **************/


    /**************  计算Y值 begin **************/
    // 求出行号
    NSUInteger row = index / colsPerRow;
    // 每一行之间的间距
    CGFloat yMargin = 20;
    // 商品Y
    CGFloat bagY = (bagH + yMargin) * row;
    /**************  计算X值 end **************/


    /**************  创建、添加商品 begin **************/
    // 创建一个商品父控件
    BagView *bagView = [BagView bagViewWithBag:self.bags[index]];
    // 设置frame
    bagView.frame = CGRectMake(bagX, bagY, bagW, bagH);
    // 将商品父控件添加到bagsView中
    [self.bagsView addSubview:bagView];
    /**************  创建、添加商品 end **************/


    /**************  设置按钮状态 **************/
    self.removeBtn.enabled = YES;
    self.addBtn.enabled = (self.bagsView.subviews.count < self.bags.count);
}

#pragma mark 删除
- (IBAction)remove {
    [self.bagsView.subviews.lastObject removeFromSuperview];

    /**************  设置按钮状态 **************/
    self.addBtn.enabled = YES;
    self.removeBtn.enabled = (self.bagsView.subviews.count > 0);
}

- (void)viewDidLoad {
    [super viewDidLoad];

    self.bagsView.clipsToBounds = YES;
}
@end
```

在这个过程中，控制器无需关心子控件视图是如何完成模型数据的接收与处理，也无需关心子控件内部的尺寸和布局，达到了封装的目的。也提高了子控件的复用性和扩展性。

### 5. MJExtension

上面已经介绍了MVC设计模式和字典转模型数据的具体应用，那么针对字典转模型这个常用的功能，能否使用一些更为便捷的方式来实现呢？这里就要说说比较流行的字典转模型框架了。

主流的字典转模型框架有三个：

- JSONModel : 所有模型都必须继承自 JSONModel


- Mantle : 所有模型都许继承自MTModel


- MJExtension

MJExtension框架是国内iOS界大牛李明杰老师([github](https://github.com/CoderMJLee/MJExtension))开发的，也是国内使用最多的一款字典转模型框架。相对另外两款框架来说，它少了一些局限，保证了Modeld的纯洁性。下面就来简单介绍MJExtension在上面的实例程序中的应用。

将MJExtension拖到工程文件中去，然后导入头文件，改写以下代码：

``` objective-c
//  ViewController.m

#import "ViewController.h"
#import "Bag.h"
#import "BagView.h"
#import "MJExtension.h"

//省略部分...

- (NSMutableArray *)bags
{
    if (！_bags) {
        _bags = [Bag objectArrayWithFilename:@"bags.plist"];
    }
    return _bags;
}

//省略部分......
```

模型文件也可删改简化：

``` objective-c
//  Bag.h

#import <Foundation/Foundation.h>

@interface Bag : NSObject
/** 图标 */
@property (nonatomic, copy)NSString *icon;
/** 名字 */
@property (nonatomic, copy)NSString *name;

@end
```

``` objective-c
//  Bag.m

#import "Bag.h"

@implementation Bag

@end
```

可以发现，使用了框架之后，数据模型的转换已经不需要在模型中添加初始化代码了，可以直接使用框架来为模型进行初始化。

MJExtension还提供了更多的使用方法，可以在直接在李明杰老师的[github](https://github.com/CoderMJLee/MJExtension)下载查看。