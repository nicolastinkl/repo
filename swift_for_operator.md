# swift for operator

自定义操作符：
```
func +* (left: Vector2D, right: Vector2D) -> Double {
    return left.x * right.x + left.y * right.y
}
```
但是编译器会给我们一个错误:

    Operator implementation without matching operator declaration

这是`因为我们没有对这个操作符进行声明`。之前可以直接重载像 +,-,* 这样的操作 符,是因为 Swift 中已经有定义了,如果我们要新加操作符的话,需要先对其进行声明, 告诉编译器这个符号其实是一个操作符。添加如下代码:

```
infix operator +* {
    associativity none
    precedence 160
}
```

- ###infix
    表示要定义的是一个中位操作符,即前后都是输入;其他的修饰子还包括 prefix 和 postfix,不再赘述

- ###associativity
    定义了结合律,即如果多个同类的操作符顺序出现的计算顺序。比如常见的加法 和减法都是 left,就是说多个加法同时出现时按照从左往右的顺序计算 (因为加 法满足交换律,所以这个顺序无所谓,但是减法的话计算顺序就很重要了)。点乘 的结果是一个 Double,不再会和其他点乘结合使用,所以这里写成 none;

- ###precedence
    运算的优先级,越高的话越优先进行运算。Swift 中乘法和除法的优先级是 150, 加法和减法是 140,这里我们定义点积优先级 160,就是说应该早于普通的乘除进 行运算。
有了这些之后,我们就可以很简单地进行向量的点积运算了:
let result = v1 +* v2

>最后需要多提一点的是,Swift 的操作符是不能定义在局部域中的,因为至少会希望在 能在全局范围使用你的操作符,否则操作符也就失去意义了。另外,来自不同 module 的操作符是有可能冲突的,这对于库开发者来说是需要特别注意的地方。如果库中的 操作符冲突的话,使用者是无法像解决类型名冲突那样通过指定库名字来进行调用的。 因此在重载或者自定义操作符时,应当尽量将其作为其他某个方法的 “简便写法”,而 避免在其中实现大量逻辑或者提供独一无二的功能。这样即使出现了冲突,使用者也 还可以用方法调用的方式使用你的库。运算符的命名也应当尽量明了,避免歧义和可 能的误解。因为一个不被公认的操作符是存在冲突风险和理解难度的,所以我们不应 该滥用这个特性。在使用重载或者自定义操作符时,请先再三权衡斟酌,你或者你的 用户是否真的需要这个的操作符。


## 操作符之自定义正则表达式：
```

struct RegexHelper {
let regex: NSRegularExpression
    init(_ pattern: String) {
        var error: NSError?
        regex = NSRegularExpression(pattern: pattern,
                                    options: .CaseInsensitive,
                                      error: &error)
}
    func match(input: String) -> Bool {
        let matches = regex.matchesInString(input,
                        options: nil,
                        range: NSMakeRange(0, countElements(input)))
return matches.count > 0 }
}

let mailPattern = "^([a-z0-9_\\.-]+)@([\\da-z\\.-]+)\\.([a-z\\.]{2,6})$"
let matcher = RegexHelper(mailPattern)
let maybeMailAddress = "onev@onevcat.com"
if matcher.match(maybeMailAddress) { println("有效的邮箱地址")
}
```


`自定义：`
```
infix operator =~ {
    associativity none
    precedence 130
}
func =~(lhs: String, rhs: String) -> Bool { return
    RegexHelper(rhs).match(lhs)
}

```
`用法:`
```

if "onev@onevcat.com" =~ "^([a-z0-9_\\.-]+)@([\\da-z\\.-]+)\\.([a-z\\.]{2,6})$" { println("有效的邮箱地址")
}
```

## 模式匹配

```
prefix operator ~/ {}
prefix func ~/(pattern: String) -> NSRegularExpression {

    return NSRegularExpression(pattern: pattern, options: nil,error: nil)

}

我们在 case 语句里使用正则表达式的话,就可以去匹配被 switch 的字符串了:
let contact = ("http://onevcat.com", "onev@onevcat.com")
let mailRegex =
~/"^([a-z0-9_\\.-]+)@([\\da-z\\.-]+)\\.([a-z\\.]{2,6})$"

let siteRegex = ~/"^(https?:\\/\\/)?([\\da-z\\.-]+)\\.([a-z\\.]{2,6})([\\/\\w \\.-]*)*\\/?$"
switch contact {
    case (siteRegex, mailRegex):
        println("同时拥有有效的网站和邮箱")
    case (_, mailRegex):
        println("只拥有有效的邮箱")
    case (siteRegex, _):
        println("只拥有有效的网站")
    default: println("嘛都没有")
}
```
