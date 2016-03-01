# @autorelease #
1. 用对象调用autorelease来延期释放这一操作必须在释放池中进行,在外面无法使用  
2. 应用场景:在类方法中用于快速创建一个对象
```objc
// 声明实现一个类方法
+ (Student *)student
{
  // 在里面直接进行autorelease
    return [[[Student alloc] init] autorelease];
}
// 在自动释放池中使用类方法创建对象
@autoreleasepool{
// 此时创建出来的对象不用关注释放问题。
Student *s = [Student student];//这里的类方法student中包含了autorelease,所以一定要放在autoreleasepool中
}
```
![另一种较为灵活的应用](http://ww2.sinaimg.cn/large/9da1eeefjw1f1e9hro5tpj21780hmdi1.jpg)
```objc
//避免将return的值赋给了错误类型的指针,可以这样改进
//instancetype：可以动态判断返回的类型和接受的类型是否一致
+ (instancetype)student{

  return [[self alloc] init];
}
  NSString *s = [Student student];//这时编译器就会警告,如果用id类型,编译器不会警告,但在运行时会报错
```

```objc
//应用类方法创建对象,同时调用对象方法初始化变量为所给参数
-(instancetype)initWithAge:(int)age{

    if (self = [super init]) {
            _age = age;
    }
        return self;
}
+(instancetype)studentWithAge:(int)age{
         return [[[self alloc] initWithAge:age] autorelease];
}
@end
```
# ARC 强指针 若指针 #
**- 类似于firstName和self.textField.text这样的指针使用关键字strong进行标志,它意味着只要该指针指向某个对象,那么这个对象就不会被销毁。反过来说,ARC的一个基本规则即是,只要某个对象被任一strong指针指向,那么它将不会被销毁。如果对象没有被任何strong指针指向,那么就将被销毁。在默认情况下,所有的实例变量和局部变量都是strong类型的。可以说strong类型的指针在行为上和MRC时代retain的property是比较相似的。既然有strong,那肯定有weak，weak类型的指针也可以指向对象,但是并不会持有该对象。
- 这里声明了一个weak的指针weakName,它并不持有@“onevcat"。如果self.textField.text的内容发生改变的话,根据之前􏰀到的"只要某个对象被任一strong指针指向,那么它将不会被销毁。如果对象没有被任何strong指针指向,那么就将被销毁”原则,此时指向@“onevcat"的指针中没 有strong类型的指针,@"onevcat"将被销毁。

- 同时,在ARC机制作用下,所有指向这个对象的weak指针将被置为nil。这个特性相当有用,相信无数的开发者都曾经被指针指向已释放对象所造成的EXC_BAD_ACCESS困扰过,使用ARC以后,不论是strong还是weak类型的指针,都不再会指向一个dealloced的对象,从根源上解决了意外释放导致的崩溃。
```objc
ARC中的@property
strong : 用于OC对象, 相当于MRC中的retain
weak : 用于OC对象, 相当于MRC中的assign
assign : 用于基本数据类型, 跟MRC中的assign一样
copy : 一般用于NSString, 跟MRC中的copy一样

在ARC情况下解决”循环retain”的问题:@property一边用strong,一边用weak。

在ARC情况下:
1) 不允许调用release,retain,retainCount
2）允许重写dealloc,但是不允许调用[super dealloc]
3) 只要弱指针指向的对象不在了,就直接把弱指针做清空(赋值为nil)操作
4) 自动释放池不建议方占用内存过大的对象或者存在大量循环的语句
@property(nonatomic,strong)Dog *dog; // 意味着生成的成员变量_dog是一个强指针,相当于以前的retain。
```
# ARC 与 MRC 的兼容问题 #
1. 如果工程为开启ARC的工程,添加MRC的文件时需要进行设置 工程-> Build Phases -> Compile Sources -> 选择MRC文件 -> 双击后输入  -fno-objc-arc  此时该文件编译时不会调用ARC功能  
2. MRC转换为ARC, Edit-> Convert -> To Objective-C ARC -> 选择对应的项目
# 分类 #
```objc
1) 当分类与主类中都有同一个方法时，优先调用分类中的方法。
2) 当多个分类中都有同样的一个方法时，优先调用最后一个参与编译的分类中的方法,查看最后一个方法可以看Build Phases-> Compile Sources里面最下面的文件
3) 在分类中用@property只会生成set与get方法,不会生成成员变量 
4) 分类可以通过self.来访问本类的成员变量
5) 分类也叫非正式协议,其作用的很大一部分是为了对iOS原有的类进行扩展,因为系统的实现是不公开的,要对系统的某个类进行扩展,使用分类
6) 其实非正式协议是与协议的对比,其本质是父类声明方法由子类实现,也就是从父类规定需要什么方法,在子类中实现  
[str characterAtIndex:8];//取出str字符串中第八个字符
```
# 类扩展 #
2、类扩展不仅可以增加方法,还可以增加实例变量(或者合成属性),只是该实例变量默认是私有类型的(作用范围只能在自身类,而不是子类或其他地方);  
3、类扩展中声明的方法没被实现,编译器会报警,但是类别中的方法没被实现编译器是不会有任何警告的。这是因为类扩展是在编译阶段被添加到类中,而类别是在运行时添加到类中。  
4、类扩展不能像类别那样拥有独立的实现部分(@implementation部分),也就是说,类扩展所声明的方法必须依托对应类的实现部分来实现。  
5、定义在 .m 文件中的类扩展方法为私有的,定义在 .h 文件(头文件)中的类扩展方法为公有(但成员变量是私有的)。类扩展是在 .m 文件中声明私有方法的非常好的方式。  
6. 以后使用基本都是在.m文件中写类扩展,类扩展中不论是@property(生成了set和get方法,但是子类无论直接访问还是通过调用子类方法访问都无法访问,即子类怎样都无法访问父类的类扩展中的变量和方法)还是直接定义的变量都是私有的  

# Block #
```objc
void (^block名称)(参数类型列表) = ^(参数列表){     //参数列表没有的时候可以不写括号
    // 代码实现；

};
block名称(参数);//调用

void (^myBlock)() =  ^{
NSLog(@“******”);
NSLog(@“******”);
}；
myBlock();

//定义一个block,计算两个整数的和
int (^ sumBlock)(int,int) = ^(int num1, int num2){
 return num1 + num2;
};
int c = sumBlock(10,20);
NSLog(@“%d”,c);


Block的定义是可以放在函数里面的,且定义后不调用则不会使用(延迟调用)
```

