# swift Closure Expression(BLOCKS)
>闭包表达式（Closure Expression）

闭包（closure） 表达式可以建立一个闭包（在其他语言中也叫 lambda, 或者 匿名函数（anonymous function））. 跟函数（function）的声明一样， 闭包（closure）包含了可执行的代码（跟方法主体（statement）类似） 以及接收（capture）的参数。 它的形式如下：

```
{（parameters） -> return type in
    statements
}
```
闭包的参数声明形式跟方法中的声明一样, 请参见：Function Declaration.

闭包还有几种特殊的形式, 让使用更加简洁：

闭包可以省略 它的参数的type 和返回值的type. 如果省略了参数和参数类型，就也要省略 'in'关键字。 如果被省略的type 无法被编译器获知（inferred） ，那么就会抛出编译错误。
闭包可以省略参数，转而在方法体（statement）中使用 $0, $1, $2 来引用出现的第一个，第二个，第三个参数。
如果闭包中只包含了一个表达式，那么该表达式就会自动成为该闭包的返回值。 在执行 'type inference '时，该表达式也会返回。
下面几个 闭包表达式是 等价的：

```
myFunction {
    （x: Int, y: Int） -> Int in
    return x + y
}

myFunction {
    （x, y） in
    return x + y
}

myFunction { return $0 + $1 }

myFunction { $0 + $1 }
```

关于 向闭包中传递参数的内容，参见： Function Call Expression.

闭包表达式可以通过一个参数列表（capture list） 来显式指定它需要的参数。 参数列表 由中括号 [] 括起来，里面的参数由逗号','分隔。一旦使用了参数列表，就必须使用'in'关键字（在任何情况下都得这样做，包括忽略参数的名字，type, 返回值时等等）。

在闭包的参数列表（ capture list）中， 参数可以声明为 'weak' 或者 'unowned' .

```
myFunction { print（self.title） }                    // strong capture
myFunction { [weak self] in print（self!.title） }    // weak capture
myFunction { [unowned self] in print（self.title） }  // unowned capture
```

在参数列表中，也可以使用任意表达式来赋值. 该表达式会在 闭包被执行时赋值，然后按照不同的力度来获取（这句话请慎重理解）。（captured with the specified strength. ） 例如：

```
// Weak capture of "self.parent" as "parent"
myFunction { [weak parent = self.parent] in print（parent!.title） }
```
