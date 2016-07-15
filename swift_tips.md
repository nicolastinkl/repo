# swift tips 初级

##Selector
@selector 是 Objective-C 时代的一个关键字,它可以将一个方法转换并赋值给一个 SEL 类型,它的表现很类似一个动态的函数指针。在 Objective-C 时 selector 非常常用,从设 定 target-action,到自举询问是否响应某个方法,再到指定接受通知时需要调用的方法 等等,都是由 selector 来负责的

在 Swift 中没有 @selector 了,我们要生成一个 selector 的话现在只能使用字符串。Swift 里对应原来 SEL 的类型是一个叫做 Selector 的结构体,它提供了一个接受字符串的初 始化方法

另外,因为 Selector 类型实现了 StringLiteralConvertible,因此我们甚至可以不使用 它的初始化方法,而直接用一个字符串进行赋值,就可以完成创建了。

最后需要注意的是,selector 其实是 Objective-C runtime 的概念,如果你的 selector 对应 的方法只在 Swift 中可见的话 (也就是说它是一个 Swift 中的 private 方法),在调用这个 selector 时你会遇到一个 unrecognized selector 错误

参考:
- [Objective-C的动态特性](http://blog.leezhong.com/ios/2013/08/03/dynamic-tips-and-tricks-with-objective-c.html)
- [Interacting with Objective-C APIs](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-XID_26)
- [Objective-C的消息传递机制](http://www.keakon.net/2011/08/10/Objective-C%E7%9A%84%E6%B6%88%E6%81%AF%E4%BC%A0%E9%80%92%E6%9C%BA%E5%88%B6)
- [深入浅出Cocoa之 Method Swizzling](http://blog.csdn.net/kesalin/article/details/7178871)

##柯里化 (Currying)

Swift 里可以将方法进行`柯里化 (Currying)`,也就是把接受多个参数的方法变换成接受 第一个参数的方法,并且返回接受余下的参数并且返回结果的新方法.
```
func addTwoNumbers(a: Int)(num: Int) -> Int {
    return a + num
}

//然后通过只传入第一个括号内的参数进行调用,这样将返回另一个方法:
let addToFour = addTwoNumbers(4) // addToFour 是一个 Int
-> Int

let result = addToFour(num: 6) // result = 10

```
参考：
- [Instance Methods are Curried Functions in Swift](http://oleb.net/blog/2014/07/swift-instance-methods-curried-functions/?utm_campaign=iOS_Dev_Weekly_Issue_157&utm_medium=email&utm_source=iOS%2BDev%2BWeekly)

##@UIApplicationMain
在 C 系语言中,程序的入口都是 main 函数。对于一个 Objective-C 的 iOS app 项目,在 新建项目时,Xcode 将帮我们准备好一个 main.m 文件,其中就有这个 main 函数:

```
int main(int argc, char * argv[]) {
    @autoreleasepool {
            return UIApplicationMain(argc, argv, nil,
                   NSStringFromClass([AppDelegate class]));
    }
}
```
在这里我们调用了 UIKit 的 UIApplicationMain 方法。这个方法将根据第三个参数初始 化一个 UIApplication 或其子类的对象并开始接收事件 (在这个例子中传入 nil,意味使 用默认的 UIApplication)。最后一个参数指定了 AppDelegate 类作为应用的委托,它被 用来接收类似 didFinishLaunching 或者 didEnterBackground 这样的与应用生命周期相关 的委托方法。另外,虽然这个方法标明为返回一个 int,但是其实它并不会真正返回。 它会一直存在于内存中,直到用户或者系统将其强制􏰄止。

了解了这些后,我们就可以来看看 Swift 的项目中对应的情况了。新建一个 Swift 的 iOS app 项目后,我们会发现所有文件中都没有一个像 Objective-C 时那样的 main 文件,也 不存在 main 函数。唯一和 main 有关系的是在默认的 AppDelegate 类的声明上方有一个 @UIApplicationMain 的标签。

不说可能您也已经猜到,这个标签做的事情就是将被标注的类作为委托,去创建一个 UIApplication 并启动整个程序。在编译的时候,编译器将寻找这个标记的类,并自动 插入像 main 函数这样的模板代码。我们可以试试看把 @UIApplicationMain 去掉会怎么 样:
```
ld: entry point (_main) undefined. for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

和 C 系语言的 main.c 或者 main.m 文 件一样,Swift 项目也可以有一个名为 main.swift 特殊的文件。在这个文件中,我们不 需要定义作用域,而可以直接书写代码。这个文件中的代码将作为 main 函数来执行。 比如我们在删除 @UIApplicationMain 后,在项目中添加一个 main.swift 文件,然后加上 这样的代码:
```
UIApplicationMain(C_ARGC, C_ARGV, nil,
    NSStringFromClass(AppDelegate))
```
现在编译运行,就不会再出现错误了。当然,我们还可以通过将第三个参数替换成自 己的 UIApplication 子类,这样我们就可以轻易地做一些控制整个应用行为的事情了。 比如将 main.swift 的内容换成:

现在编译运行,就不会再出现错误了。当然,我们还可以通过将第三个参数替换成自 己的 UIApplication 子类,这样我们就可以轻易地做一些控制整个应用行为的事情了。 比如将 main.swift 的内容换成:
```
import UIKit
class MyApplication: UIApplication {
override func sendEvent(event: UIEvent!) {
        super.sendEvent(event)
        println("Event sent: \(event)");
    }
}
UIApplicationMain(C_ARGC, C_ARGV,
    NSStringFromClass(MyApplication), NSStringFromClass(AppDelegate))
```
这样每次发送事件 (比如点击按钮) 时,我们都可以监听到这个事件了。

使用场景：比如语音或者audio录音开启时，需要用到系统函数和事件，那么就需要重写这里了。




##初始化方法顺序

```
class Cat {
    var name: String
    init() {
name = "cat" }
}
class Tiger: Cat {
    let power: Int
    override init() {
        power = 10
        super.init()
        name = "tiger"
    }
}

```
与 Objective-C 不同,Swift 的初始化方法需要保证类型的所有属性都被初始化。所以初 始化方法的调用顺序就很有讲究。在某个类的子类中,初始化方法里语句的顺序并不 是随意的,我们需要保证在当前子类实例的成员初始化完成后才能调用父类的初始化 方法。
一般来说,子类的初始化顺序是:
1. 设置子类自己需要初始化的参数,power = 10
2. 调用父类的相应的初始化方法,super.init()
3. 对父类中的需要改变的成员进行设定,name = "tiger"

其中第三步是根据具体情况决定的,如果我们在子类中不需要对父类的成员做出改变 的话,就不存在第 3 步。而在这种情况下,Swift 会自动地对父类的对应 init 方法进行 调用,也就是说,第 2 步的 super.init() 也是可以不用写的 (但是实际上还是调用的, 只不过是为了简便 Swift 帮我们完成了)。这种情况下的初始化方法看起来就很简单
```
class Cat {
    var name: String
    init() {
name = "cat" }
}
class Tiger: Cat {
    let power: Int
    override init() {
        power = 10
// 虽然我们没有显式地对 super.init() 进行调用
// 不过由于这是初始化的最后了,Swift 替我们完成了 }
}
```

##Designated,Convenience 和 Required

所以 Swift 有了超级严格的初始化方法。一方面,Swift 强化了 designated 初始化方法的 地位。Swift 中不加修饰的 init 方法都需要在方法中保证所有非 Optional 的实例变量被 赋值初始化,而在子类中也强制 (显式或者隐式地) 调用 super 版本的 designated 初始化, 所以无论如何走何种路径,被初始化的对象总是可以完成完整的初始化的。
```
class ClassA {
    let numA: Int
    init(num: Int) {
        numA = num
} }
class ClassB: ClassA {
    let numB: Int
    override init(num: Int) {
        numB = num + 1
        super.init(num: num)
    }
}
```
在上面的示例代码中,注意在 init 里我们可以对 let 的实例常量进行赋值,这是初始 化方法的重要特点。在 Swift 中 let 声明的值是不变量,无法被写入赋值,这对于构建 线程安全的 API 十分有用。而因为 Swift 的 init 只可能被调用一次,因此在 init 中我 们可以为不变量进行赋值,而不会引起任何线程安全的问题。

与 designated 初始化方法对应的是在 init 前加上 convenience 关键字的初始化方法。这 类方法是 Swift 初始化方法中的“二等公民”,只作为补充和提供使用上的方便。所有 的 convenience 初始化方法都必须调用同一个类中的 designated 初始化完成设置,另外 convenience 的初始化方法是不能被子类重写或者是从子类中以 super 的方式被调用的。

参考资料:
- [http://stackoverflow.com/questions/8056188/should- i- refer- to- self- property- in- the- init- method- with- arc](http://stackoverflow.com/questions/8056188/should- i- refer- to- self- property- in- the- init- method- with- arc)

- [https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html)

    ```
    class ClassA {
    let numA: Int
    init(num: Int) {
        numA = num
}
    convenience init(bigNum: Bool) {
        self.init(num: bigNum ? 10000 : 1)
} }
class ClassB: ClassA {
    let numB: Int
    override init(num: Int) {
        numB = num + 1
        super.init(num: num)
    }
}
    ```
只要在子类中实现重写了父类 convenience 方法所需要的 init 方法的话,我们在子 类中就也可以使用父类的 convenience 初始化方法了。比如在上面的代码中,我们在 ClassB 里实现了 init(num: Int) 的重写。这样,即使在 ClassB 中没有 bigNum 版本的 convenience init(bigNum: Bool),我们仍然还是可以用这个方法来完成子类初始化:

   ` let anObj = ClassB(bigNum: true)`
    `// anObj.numA = 10000, anObj.numB = 10001`

因此进行一下总结,可以看到初始化方法永远遵循以下两个原则:
1. 初始化路径必须保证对象完全初始化,这可以通过调用本类型的 designated 初始 化方法来得到保证;
2. 子类的 designated 初始化方法必须调用父类的 designated 方法,以保证父类也完成 初始化。

对于某些我们希望子类中一定实现的 designated 初始化方法,我们可以通过添加 required 关键字进行限制,强制子类对这个方法重写实现。这样的一个最大的好处 是可以保证依赖于某个 designated 初始化方法的 convenience 一直可以被使用。一个现 成的例子就是上面的 init(bigNum: Bool):如果我们希望这个初始化方法对于子类一定 可用,那么应当将 init(num: Int) 声明为必须,这样我们在子类中调用 init(bigNum: Bool) 时就始􏰄能够找到一条完全初始化的路径了:
```
class ClassA {
    let numA: Int
    required init(num: Int) {
        numA = num
}
    convenience init(bigNum: Bool) {
        self.init(num: bigNum ? 10000 : 1)
} }
class ClassB: ClassA {
    let numB: Int
    required init(num: Int) {
        numB = num + 1
        super.init(num: num)
    }
}

```

## 结束
部分来自 ![](https://secure.gravatar.com/avatar/318643095c83b914cf80a7f99f247fe6.png?d=wavatar&r=G&s=48) [@onevcat](https://selfstore.io/products/171)

