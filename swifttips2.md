# swift tips 中级

##protocol 组合
在 Swift 中我们可以使用 Any 来表示任意类型 (如果你对此感到模糊或者陌 生的话,可以先看看 Apple 的 Swift 官方教程或者本书的这篇 tip)。充满好奇心的同学 可能已经发现,Any 这个类型的定义十分奇怪,它是一个 protocol<> 的同名类型。
protocol<> 这样形式的写法在日常 Swift 使用中其实并不多见,这其实是 Swift 的接口 组合的用法。标准的语法形式是下面这样的:
```
protocol<ProtocolA, ProtocolB, ProtocolC>
```
尖括号内是具体接口的名称,这里表示将名称为 ProtocolA,ProtocolB 以􏰁 ProtocolC 的接口组合在一起的一个新的匿名接口。实现这个匿名接口就意味着要同时实现这三 个接口所定义的内容。所以说,这里的 protocol 组合的写法和下面的新声明的 ProtocolD 是相同的:
```
protocol ProtocolD: ProtocolA, ProtocolB, ProtocolC {
}
```
那么,在 Any 定义的时候的里面什么都不写的 protocol<> 是什么意思呢?从语意上来 说,这代表一个 “需要实现空接口的接口”,其实就是任意类型的意思了。

除了可以方便地表达空接口这一概念以外,protocol 的组合相比于新创建一个接口的最 大区别就在于其匿名性。有时候我们可以借助这个特性写出更清晰的代码。因为 Swift 的类型组􏰃是比较松散的,你的类型可以由不同的 extension 来定义实现不同的接口, Swift 也并没有要求它们在同一个文件中。这样,当一个类型实现了很多接口时,在使 用这个类型的时候我们很可能在不查询相关代码的情况下很难知道这个类型所实现的 接口。

举个理想化的例子,比如我们有下面的三个接口,分别代表了三种动物的叫的方式,而 有一种谜之动物,同时实现了这三个接口:
```
protocol KittenLike {
    func meow() -> String
}
protocol DogLike {
    func bark() -> String
}
protocol TigerLike {
    func aou() -> String
60
protocol 组合 61 }
class MysteryAnimal: CatLike, DogLike, TigerLike {
    func meow() {
return "meow" }
func bark() {
return "bark" }
func aou() {
return "aou" }
}
```
现在我们想要检查某种动物作为宠物的时候的叫声的话,我们可能要重新定义一个 叫做 PetLike 的接口,表明其实现 KittenLike 和 DogLike;如果稍后我们又想检查某 种动物作为猫科动物的叫声的话,我们也许又要去定义一个叫做 CatLike 这样的实现 KittenLike 和 TigerLike 的接口。最后我们大概会写出这样的东西:
```
protocol PetLike: KittenLike, DogLike {
}
protocol CatLike: KittenLike, TigerLike {
}
struct SoundChecker {
static func checkPetTalking(pet: PetLike) {
//...
}
static func checkCatTalking(cat: CatLike) { //...
} }

```
虽然没有引入定义任何新的内容,但是为了实现这个需求,我们还是添加了两个空 protocol,这可能会让人困惑,代码的使用者 (也包括一段时间后的你自己) 可能会去猜 测 PetLike 和 CatLike 的作用 –

其实它们除了标注以外并没有其他作用。借助 protocol 组合的特性,我们可以很好的解决这个问题。protocol 组合是可以使用 typealias 来命名 的,于是可以将上面的新定义 protocol 的部分换为:

    typealias PetLike = protocol<KittenLike, DogLike>
    typealias CatLike = protocol<KittenLike, TigerLike>


这样既保持了可读性,也没有多定义不必要的新类型。
另外,其实如果这两个临时接口我们就只用一次的话,如果上下文里理解起来不会有 困难,我们完全可以直接将它们匿名化,变成下面这样:
```

struct SoundChecker {
static func checkPetTalking(pet: protocol<KittenLike, DogLike>) {
//...
}
static func checkCatTalking(cat: protocol<KittenLike, TigerLike>) { //...
} }

```


这样的好处是定义和使用的地方更加接近,这在代码复杂的时候读代码时可以少一些 跳转,多一些专注。但是因为使用了匿名的接口组合,所以能表达的信息毕竟少了一 些。如果要实际使用这种方法的话,还是需要多多斟酌。

虽然这一节已经够长了,不过我还是想多提一句关于实现多个接口时接口内方法冲突 的解决方法。因为在 Swift 的世界中没有人限制或者保证过不同接口的方法不能重名, 所以这是有可能出现的情况。比如有 A 和 B 两个接口,定义如下:
```
protocol A {
    func bar() -> Int
}
protocol B {
    func bar() -> String
}

```


两个接口中 bar() 只有返回值的类型不同。我们如果有一个类型 Class 同时实现了 A 和 B,我们要怎么才能避免和解决调用冲突呢?

```
class Class: A, B {
    func bar() -> Int {
        return 1
    }
    func bar() -> String {
        return "Hi"
    }
}

```
这样一来,对于 bar(),只要在调用前进行类型转换就可以了:


```

let instance = Class()
let num = (instance as A).bar()// 1
let str = (instance as B).bar()// "Hi"

```

##static 和 class

Apple 表示今后将会考虑在某个升级版本中实装 class 类型 的类存储变量,现在的话,我们只能在 class 中用 class 关键字声明方法和计算属性。

有一个比较特殊的是 protocol。在 Swift 中 class,struct 和 enum 都是可以实现 protocol 的。那么如果我们想在 protocol 里定义一个类型域上的方法或者计算属性的话,应该 用哪个关键字呢?答案是`使用 class 进行定义,但是在实现时还是按照上面的规则:在 class 里使用 class 关键字,而在 struct 或 enum 中仍然使用 static – 虽然在 protocol 中定义时使用的是 class`:

```

protocol MyProtocol {
    class func foo() -> String
}
struct MyStruct: MyProtocol {
    static func foo() -> String {
        return "MyStruct"
    }
}
enum MyEnum: MyProtocol {
    static func foo() -> String {
    return "MyEnum" }
}
class MyClass: MyProtocol {
    class func foo() -> String {
    return "MyClass" }
}


class someClass {
            class func  one() {
                println("OK")
            }
}


someClass.one()

```

##@objc 和 dynamic
Apple 采取的做法是允许我们在同一个项目中同时使用 Swift 和 Objective-C 来进行开 发。其实一个项目中的 Objective-C 文件和 Swift 文件是处于两个不同世界中的,为了让 它们能相互联通,我们需要添加一些桥梁。

首先通过添加 `{product-module-name}-Bridging-Header.h` 文件,并在其中填写想要使用 的头文件名称,我们就可以很容易地在 Swift 中使用` Objective-C `代码了。Xcode 为了简 化这个设定,甚至在 Swift 项目中第一次导入 Objective-C 文件时会主动弹框进行询问是 否要自动创建这个文件,可以说是非常方便。

但是如果想要在 Objective-C 中使用 Swift 的类型的时候,事情就复杂一些。如果是来自 外部的框架,那么这个框架与 Objective-C 项目肯定不是处在同一个 target 中的,我们 需要对外部的 `Swift module `进行导入。这个其实和使用 Objective-C 的原来的 Framework 是一样的,对于一个项目来说,外界框架是由 Swift 写的还是 Objective-C 写的,两者并 没有太大区别。我们通过使用 2013 年新引入的 @import 来引入 module:`@import MySwiftKit;`

之后就可以正常使用这个 Swift 写的框架了。
如果想要在 Objective-C 里使用的是同一个项目中的 Swift 的源文件的话,可以直接导 入自动生成的头文件` {product-module-name}-Swift.h` 来完成。比如项目的 target 叫做 MyApp 的话,我们就需要在 Objective-C 文件中写

    #import "MyApp-Swift.h"

但这只是故事的开始。Objective-C 和 Swift 在底层使用的是两套完全不同的机制,Cocoa 中的 Objective-C 对象是基于运行时的,它从骨子里遵循了 KVC (Key-Value Coding,通 过类似字典的方式存储对象信息) 以􏰁动态派发 (Dynamic Dispatch,在运行调用时再决 定实际调用的具体实现)。而 Swift 为了追求性能,如果没有特殊需要的话,是不会在运 行时再来决定这些的。也就是说,Swift 类型的成员或者方法在编译时就已经决定,而 运行时便不再需要经过一次查找,而可以直接使用。

显而易见,这带来的问题是如果我们要使用 Objective-C 的代码或者特性来调用纯 Swift 的类型时候,我们会因为找不到所需要的这些运行时信息,而导致失败。解决起来也 很简单,在 Swift 类型文件中,我们可以将需要暴露给 Objective-C 使用的任何地方 (包 括类,属性和方法等) 的声明前面加上 @objc 修饰符。注意这个步骤只需要对那些不是 继承自 NSObject 的类型进行,如果你用 Swift 写的 class 是继承自 NSObject 的话,Swift


会默认自动为所有的非 private 的类和成员加上 @objc。这就是说,对一个 NSObject 的
子类,你只需要导入相应的头文件就可以在 Objective-C 里使用这个类了。

`@objc` 修饰符的另一个作用是为 Objective-C 侧重新声明方法或者变量的名字。虽然绝大 部分时候自动转换的方法名已经足够好用 (比如会将 Swift 中类似 `init(name: String)` 的 方法转换成

    -initWithName:(NSString *)name

这样),但是有时候我们还是期望 Objective- C 里使用和 Swift 中不一样的方法名或者类的名字,比如 Swift 里这样的一个类:


```
class 我的类 {
func 打招呼(名字: String) {
    println("哈喽,\(名字)") }
}
我的类().打招呼("小明")

```
Objective-C 的话是无法使用中文来进行调用的,因此我们必须使用 @objc 将其转为 ASCII 才能在 Objective-C 里访问:

```
@objc(MyClass)
class 我的类 {
@objc(greeting:)
func 打招呼(名字: String) {
    println("哈喽,\(名字)") }
}

```
这样,我们在 Objective-C 里就能调用 [[MyClass new] greeting:@"XiaoMing"] 这样的代 码了 (虽然比起原来一点都不好玩了)。另外,正如上面所说的以􏰁在 Selector 一节中所 提到的,即使是 NSObject 的子类,Swift 也不会在被标记为 private 的方法或成员上自 动加 @objc。如果我们需要使用这些内容的动态特性的话,我们需要手动给它们加上 @objc 修饰。

添加 @objc 修饰符并不意味着这个方法或者属性会变成动态派发,Swift 依然可能会将 其优化为静态调用。如果你需要和 Objective-C 里动态调用时相同的运行时特性的话, 你需要使用的修饰符是 dynamic。一般情况下在做 app 开发时应该用不上,但是在施展 一些像动态替换方法或者运行时再决定实现这样的 “黑魔法” 的时候,我们就需要用到 dynamic 修饰符了。在 KVO 一节中,我们提到了一个关于使用 dynamic 的实例。

## 结束
部分来自 ![](https://secure.gravatar.com/avatar/318643095c83b914cf80a7f99f247fe6.png?d=wavatar&r=G&s=48) [@onevcat](https://selfstore.io/products/171)
