# 删除Xcode中的临时文件 #
- 写循环体时如果没有思路,可以先写前三次循环然后总结规律  
![临时文件路径](http://ww3.sinaimg.cn/large/9da1eeefjw1f1esqwmbuaj217u0jitgs.jpg)
- 一般在函数方法的开始注释,说明函数的功能,返回值的含义,参数的含义,算法,函数中一般不注释,除非算法比较晦涩  
- 一般拿到一个新的类,先看一下initWith方法有哪些,再转到头文件看看方法  
# Foundation框架 #
```objc
核心三大库:字符串 集合 文件
字符串:(NSString,NSMutableString)
集合:(数组:NSArry,NSMutableArry;键值对:NSDictionary,NSMutableDictionary)
文件:(NSFileManager)

其他类:时间类(NSDate) 包装类(NSNumber,NSValue) 简单辅助使用类(NSURL)
```
# NSString #
- utf-8编码,中文3字节,英文1字节  
## NSError initWithContentsOfFile ##
**三个参数**  
1. 文件路径,字符串,全路径  
2. 文件编码格式,mac下默认为utf-8格式,编码为4或者NSUTF8StringEncoding  
3. 错误信息对象的指针,如果出现错误,在方法中会生成错误对象,赋值给该指针  

- initWithContentsOfString中的最后一个参数是NSError **类型,为二级指针  
- 一级指针指向变量,指针内存储变量的地址,二级指针则指向地址  
- 所以定义该参数时`NSError *err` 传递参数时用取地址符  
- 出现错误信息,err被赋值,指向一个错误对象,后可以直接打印err即可显示错误信息  
- 没有出现错误时,err中为null  

### 函数的值传递和引用传递 ###
- 值传递的本质是赋值传递的参数的值给函数内变量,与函数外被复制的参数无关
- 引用传递传递的是参数的内存地址,函数内对参数更改会直接改变参数的值
```objc
Person * p = [Person new];
这里 p 是一个指针变量,他的类型为 Preson * 类型,
他指向的变量的类型为Person类型([Person new]为Person类型的对象),他存储的内容是[Person new]的地址 即 p = &[Person new];

Person * * a =  p = &[Person new];
这里的a是一个二级指针变量,他的类型为Person * * 类型,
他指向的变量的类型为Person * 类型(p为Person * 类型的指针变量,&[Person new]与p存储的内容相同,都为内存地址),
他存储的内容是p的地址,即 a = &p = &&[Person new];
也就是说二级指针存放的一般为指针的地址,也就是一级指针存放的内容的地址的地址(应该是不存在地吧?)
```
----  

##URL和initWithContentsOfUrl ##
**URL**
1. 协议://主机:端口/路径?参数  
2. http超文本传输协议 ftp文件传输协议 file文件目录  
## initWithFormat NSLog ##
  
## initWithUTF8String ##
## (NSString * )stringByTrimmingCharactersInset:(NSCharacterSet*)set ##
## NSMutableString ##
- 增(append,add) 删(delete,remove) 插(insert) 改(update,replace) 查(range)
- initWithString  


