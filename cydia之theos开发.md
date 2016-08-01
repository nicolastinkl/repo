### Tweak 入侵和破解技术（微信红包）

在iOS的黑客界，要做破解或越狱开发，就必须了解tweak，它是各种破解补丁的统称，在google上，如果你想搜索一些越狱开发资料或者开源的破解补丁代码，它是最好的关键字。

iOS的tweak大致分为两种：

* 第一种是在cydia上发布的，需要越狱才能安装，大部分是deb格式的安装包，iOS在越狱后，会默认安装一个名叫mobilesubstrate的动态库，它的作用是提供一个系统级的入侵管道，所有的tweak都可以依赖它来进行开发，目前主流的开发工具有theos和iOSOpenDev，前者是采用makefile的一个编译框架，后者提供了一套xcode项目模版，可以直接使用xcode开发可调试，但是这个项目已经停止更新了，对高版本的xcode支持不好，大家酌情选择（本文中的例子全部采用theos）

* 第二种是直接打包成ipa安装包，并使用自己的开发证书或者企业证书签名，不需越狱也可以安装，可直接放到自己的网站上，可实现在线安装；对于没有越狱的手机，由于权限的限制，我们是没有办法写系统级的tweak的，例如springboard的补丁是没法运行的，这种tweak大多是针对某个app，把目标app进行修改注入处理，再重新签名和发布，有点类似于windows软件的xxx破解版、xxx免注册版


没有越狱的机器由于系统中没有mobilesubstrate这个库，我们有二个选择，第一个是直接把这个库打包进ipa当中，使用它的api实现注入，第二个是直接修改汇编代码；第一个适用于较为复杂的破解行为，而且越狱tweak代码可以复用，第二种适用于破解一些if…else…之类的条件语句

### Mobilesubstrate

下面的图展示的就是oc届著名的method swizzling技术，他就是iOS的注入原理，类似于windows的钩子，所以我们注入也称为hook

![](http://img.blog.csdn.net/20160630192139249)

Mobilesubstrate为了方便tweak开发，提供了三个重要的模块：

* **MobileHooker** 就是用来做上面所说的这件事的，它定义一系列的宏和函数，底层调用objc－runtime和fishhook来替换系统或者目标应用的函数

* **MobileLoader** 用来在目标程序启动时根据规则把指定目录的第三方的动态库加载进去，第三方的动态库也就是我们写的破解程序，他的原理下面会简单讲解一下

* **Safe mode** 类似于windows的安全模式，比如我们写的一些系统级的hook代码发生crash时，mobilesubstrate会自动进入安全模式，安全模式下，会禁用所有的第三方动态库


### app注入原理

上面讲到了mobileloader，他是怎么做到把第三方的lib注入进目标程序的呢？这个我们要从二进制文件的结构说起，从下面的图来看，Mach-O文件的数据主体可分为三大部分，分别是头部（Header）、加载命令（Load commands）、和最终的数据（Data）。mobileloader会在目标程序启动时，会根据指定的规则检查指定目录是否存在第三方库，如果有，则会通过修改二进制的loadCommands，来把自己注入进所有的app当中，然后加载第三方库。

![](http://img.blog.csdn.net/20160630194435735)

为了让大家看的更清楚，下面我用machoview来打开一个真实的二进制文件给大家看看，可以看出，二进制当中所有引用到的动态库都放在Load commands段当中，所以，通过给这个段增加记录，就可以注入我们自己写的动态库了.

![](http://img.blog.csdn.net/20160630195027509)

那么问题来了，在这里插入我们自己的动态库有什么用？我们自己写的代码没有执行的入口，我们一样没发干坏事，嗯，恭喜你问到点子上了，我们还需要一个”main”函数来执行我们自己的代码，这个”main”函数在oc里面称为构造函数，只要在函数前声明 “attribute\(\(constructor\)\) static” 即可，有了它我们就可以发挥想象力，进行偷天换日干点坏事了：

```c
#import <CaptainHook/CaptainHook.h> CHDeclareClass(AnAppClass); CHMethod(1, void, AnAppClass, say, id, arg1) { NSString* tmp=@"Hello, iOS!"; CHSuper(1, AnAppClass, say, tmp); } __attribute__((constructor)) static void entry() { NSLog(@"Hello, Ice And Fire!"); CHLoadLateClass(AnAppClass); CHClassHook(1, AnAppClass,say); }
```





到这里为止，我们已经知道了怎么在目标程序注入自己的代码，那么我们怎么知道需要hook哪些方法？怎么找到关键点进行实际的破解呢？下面讲一下常见的app入侵分析方法

### iOS逆向分析方法

逆向分析最常用的有三种方法：

1. **网络分析** 通过分析和篡改接口数据，可以有效的破解通过接口数据来控制客户端行为的app，常用的抓包工具有Tcpdump, WireShark, Charles等，windows平台有fidller

2. **静态分析** 通过砸壳、反汇编、classdump头文件等技术来分析app行为，通过这种方式可以有效的分析出app实用的一些第三方库，甚至分析出app的架构等内容，常用的工具有dumpdecrypted（砸壳）、hopper disassembler（反汇编）、class\_dump（导头文件）

3. **动态分析** 有静就有动，万物都是相生相克的，动态分析指的是通过分析app的运行时数据，来定位注入点或者获取关键数据，常用的工具有cycript（运行时控制台）、 lldb+debugserver（远程断点调试）、logify（追踪）




