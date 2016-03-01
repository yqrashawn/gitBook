# 复习 #
1. 做框架才会很大涉及内存管理,面试也会用到  
2. 面向对象在较复杂的程序中能够使其便于维护管理升级,但现阶段代码难度小体会不到其优势  
# block 代码块的基本使用 #
1. 跟函数指针一样,相当于一个可执行的函数,理解为block就是函数  
2. ^ hat  
3. 对block进行重命名
```objc
typedef int num;//用num定义的变量为int类型
num shuZi;

struct Person {...}p;//定义一个Person结构体类型的变量p
typedef struct Person {...}p;//定义一个类型p,由该类型声明的变量都是Person结构体类型的

typedef void (^abc)(); //由abc声明的变量是无参数无返回值的block类型
abc block1;
block1 = ^{.....};//实现

abc (^block2)() = ^{......}; //实现了一个返回值为无参数无返回值的block类型的block类型

abc (^block2);
block2 = ^{......};
```
4. block的使用,一般作为方法函数的返回值或参数使用,主要作为参数使用,作用是使得函数可装配(因原本函数和方法中无法定义新的函数或方法)  
5.C语言中如果内部定义了与外部同名的变量,外部变量在内部无效,该结论对block也成立  
6.block访问外部变量
![block访问外部变量](http://ww4.sinaimg.cn/large/9da1eeefjw1f1e0cru5o3j216y0rg0yz.jpg)
![block访问外部变量时内存中的原理](https://ooo.0o0.ooo/2016/02/27/56d16ab64e205.png)
- num原本存储在栈区,当函数内部创建block时,会产生新的栈区,函数会将block拷贝到堆区,block从堆区中读取num,需要注意的是num不是block的参数,这里block无法更改num的值(用_  __block修饰就可以在block中修改)  
- 打印两个num的地址可以看出,block中的num的地址已经改变  
- `__block int num = 123;`这时num变量就可以在堆中修改,变为堆命名变量
![在block中修改外部变量值的地址变化](http://ooo.0o0.ooo/2016/02/27/56d16e75225b4.png)
![不在block中修改外部变量值时的地址变化](http://ooo.0o0.ooo/2016/02/27/56d16ed7f077d.png)
- 可以看出,当没有在block中修改外部变量时,只有block内部的num地址是堆地址,而一旦修改了num,则从进入block开始后的变量地址都是堆地址  
- 加了 __ __block后从block定义后所有访问变量都是堆区变量  
7. 调用block时不能传入空 nil  
8. 在block中也可以定义与外部同名的变量,也能在block内覆盖外部变量  
```objc
typedef void (^Block)(int c,int d);

//block有参数
void func(int a, int b, Block myblock);
func(int a, int b,^(int a,float b){
    //block实现
    });
    {
        //func实现
}
//即在使用func的时候在实现block,而不是在定义func时



typedef int (^Block)(int c,int d);

void func(int a, int b, Block myblock);

void func(int a, int b, (int (^)(int c, int d))myblock);//也可以这样定义



//即,定义时可以先重命名block再定义以block为参数的函数或方法,也可以直接定义函数或方法时直接定义block,但实现block一定要在使用函数或方法时实现,
//如果有其他需求无法在这时实现,则要单独找位置实现,不能(无法)在声明函数和方法时实现
```
# protocol #
```objc
1）把同一类方法，放到1个协议当中
2）1个类可以同时遵守多个协议。
3）父类遵守了某个协议，子类也遵守了该协议。
4）协议遵守协议：1个协议可以遵守多个协议
@protocol SportProtocol <NSObject>
@end

 * 此处的NSObject就是基协议，意义等同于继承NSObject。
 * 继承基协议的好处：能够拥有基协议当中所声明的所有方法。

总结：
1、需要添加成员变量和方法时，建议使用继承
2、只需要添加方法时，建议使用分类
3、当很多不同类中，有共同的方法可以抽取时，类之间不存在继承关系，建议使用协议。

@protocol中可以添加修饰词
@required：用@required修饰的方法，必须实现。
@optional：用@optional修饰的方法，可实现，可不实现。
@required 和 @optional的主要意义:一般用于序员交流用。

@protocol还可以用在定义变量创建对象时,对他们进行约束

id d = [[Person alloc] init];
要求：变量d指向的对象必须遵守PlayProtocol协议
id<PlayProtocol> d = [[Person alloc] init];

要求：要求Person创建出来的对象，必须遵守PlayProtocol协议。
Person < PlayProtocol > *p = [[Person alloc] init];

真实使用场景:
@interface Person :NSObject <PlayProtocol>
@end
这样创建的任何对象，都拥有协议当中的方法。

文件中可以像@class一样使用@protocol而不需要引入.h文件
在.m文件中需要使用import来引入protocol的头文件
```

# id与instanstype的区别 #
```objc
创建对象：
(id)person{
     return [[Person alloc] init];
}
这样，子类无法用这个方法创建对象,因子类无法调用该对象

(id)person{
     return [[self alloc] init];

}

调用自己没有的方法，程序会崩溃
建议使用：
(instanstype)person{
     return [[self alloc] init];

}
总结：

id和instancetype的区别
1）instancetype只能作为函数或者方法的返回值
2）id能作为方法或者参数的数据类型、返回值，也能用来定义变量。
3）instancetype对比id的好处：
能精确的限制返回值的具体类型。

```
# 代理模式 #
```objc
代理模式概念
传入的对象,代替当前类完成了某个功能,称为代理模式

利用协议实现代理模式的主要思路为:

1）定义一个协议,里面声明代理类需要实现的方法列表,比如这里：一个代理类需要实现：feedBaby与HongSleep方法
2）创建一个代理类(比如BaoMu),遵守上面的代理协议
3）在需要代理的类中(Baby),定义一个对象类型为id且遵守代理协议 的成员变量。(delegate)
4）在Baby类中调用成员变量delegate(代理)的方法,调用代理类的方法。
5）main.m或其他使用Baby类的文件中,为Baby类的成员变量(代理类)赋值。
//类A->对象 类B

代理设计模式的场合:
代理模式至少由目标对象和代理对象存在
当对象A发生了一些行为,想告知对象B (让对象B成为对象A的代理对象)
对象B想监听对象A的一些行为 (让对象B成为对象A的代理对象)
当对象A无法处理某些行为的时候,想让对象B帮忙处理。(让对象B成为对象A的代理对象)

目标对象角度的代理模式:通知模型,手机中需要网络下载的功能
代理对象角度:监听模型
```

