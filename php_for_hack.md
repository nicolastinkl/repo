# php for hack

## Facebook为何要优化PHP

这个问题可以从不同角度来回答。简单直接的回答是，Facebook的规模太大了。PHP的性能问题限制了Facebook的发展。从另一个角度来回答，则是要回答既然PHP不够用，为什么不干脆换掉？

把PHP换掉也有“整体换”和“局部换”的区别。最彻底的方案就是完全离开PHP，用别的语言重写一套。但是对于Facebook而言这个代价太高了。如果切换的话，多年来在PHP的积累就完全作废了。而且Facebook的业务逻辑非常复杂，据说PHP代码有2千万行…… 而且，Facebook员工众多，切换到一种新的语言，学习成本也不低。

既然整体换不可行，那就局部换吧。例如给PHP写C/C++扩展，可以提升性能。但是PHP扩展开发起来成本高，一般只适用于比较稳定的库，适用范围很有限。另一个方案将性能瓶颈的地方用其他语言实现，然后通过RPC（Remote Procedure Call，远程过程调用）在PHP和其他语言之间通讯。Twitter就用了这条路线，大量组件使用Scala和Java编写，通过RPC与展现层的Rails通讯。事实上，Facebook在这方面已经做了不少工作，为了减少RPC调用的开销，Facebook还专门开发了Thrift。然而，C++开发成本比PHP高很多，不适合用在需要快速修改的地方，而且大量RPC调用终究会影响性能。

整体换不现实，Thrift不够用，那么Facebook优化PHP就势在必行了。

## Facebook要如何优化PHP

优化PHP，最先想到的是作性能分析，找出瓶颈，然后进行对应的优化。Facebook为此开发了XHProf工具。XHProf精确到函数层面，数据收集组件使用C开发（PHP扩展），报告组件用了PHP。支持PHP 5.2以上版本，对于定位性能瓶颈很有帮助。

但是PHP语言层面的优化限制太多，对Facebook而言还是不够用。所以Facebook需要对PHP语言的实现本身进行优化。

首先可以考虑的方案是改善PHP的官方实现。PHP的官方解释器运行PHP代码的过程可以分为两步：第一步，将PHP编译为bytecode；第二步，运行bytecode。那么改善PHP的官方实现就可以从这两个方面着手。

首先是优化编译PHP的步骤，这方面的工作已经有ZendOptimizerPlus做了。它会在内存中缓存编译好的bytecode，这样以后访问代码的时候就可以直接访问缓存好了的bytecode，省去了从磁盘读取再重新编译的开销。但是由于PHP语言的动态性，这个方法的效果一般，最好的情况下也只能提升20%的性能。

其次是优化运行bytecode的步骤。上面提到的ZendOptimizerPlus主要是优化编译PHP，但是也附带做了一些bytecode运行的优化。PHP有三种方式来运行bytecode：CALL、SWITCH和GOTO，默认使用CALL，也就是函数调用。优化函数调用，常用的方法就是内联（Inline function），也就是将函数展开，将函数体插入替换调用该函数的地方，这样可以节省每次调用函数带来的额外时间开支。但是这种做法其实是用“空间换时间”，如果内联过头了，空间开销会很大，得不偿失。在这方面进行调整，可以提升运行bytecode的性能。

此外还可以将整个PHP解释器用汇编重写，以快闻名的LuaJIT就是这么干的。

然而，无论是内联优化还是用汇编重写，代价都很大，而且如果优化官方实现的话，还要考虑PHP的向下兼容……

既然这个方案不太现实，那么不如把PHP搬到JVM上吧？JVM性能相当不错。

把PHP搬到JVM的工作，有人已经做过了。例如，IBM的P8（已死）和Quercus（半死不活）。Facebook也研究过这个方案，2012年的时候，还有Facebook迁移到JVM的传闻。其实Facebook早已放弃这条路线。根据Facebook的研究，Quercus的性能和Zend+APC相比，差不了太多。这一方案效果不理想的原因可能是，JVM本身性能的优化是针对Java做的，其他语言在JVM上实现，不一定能用到这些优化。动态语言尤为如此。因为Java本身是静态类型的，所以很多优化JVM就没必要做，而在JVM上跑的动态语言需要这些优化。

既然JVM是为Java优化的，搬上去不合适，那不如针对PHP开发一个VM？这样就可以作大量针对性地优化了。然而开发VM可没有那么容易，成本不小，所以Facebook最初的选择是将PHP编译成C/C++之类性能优异的语言。也就是HHVM的前身——HPHPc。具体的做法是将PHP翻译为C++，然后再编译。

相比VM，这样的实现比较简单，而且能放手做优化（因为是离线编译，所以可以用时间换性能）。但是PHP的很多动态内容编译成C++比较麻烦，因此HPHPc禁掉了eval()之类的特性，即使这样，还是带来了一些问题，特别是由于需要将动态include的文件都编译在一起，最终的部署文件体积太庞大了，都过G了。

和HPHPc类似的项目有Roadsend和phc，前者已经不维护了，后者也是命运坎坷。

编译到C++的效果不好，所以Facebook最终决定，还是写一个VM吧。

## HHVM

FaceBook开发HHVM的阵容相当豪华，其中包括

Andrei Alexandrescu, 《C++ Coding Standards》的作者。
Drew Paroski，改进了.NET虚拟机的JIT。
Jason Evans，jemalloc的开发者(jemalloc将Firefox的内存消耗降低了一半)。
Keith Adams，VMware核心架构。
Sara Golemon，《Extending and Embedding PHP》作者，PHP内核领域的专家。
值得注意的是，Keith Adams给HHVM的影响很大。HHVM使用了JIT技术，一般的代码通过解释器执行(因为JIT也是有开销的)，而常用的代码则使用JIT优化。通常而言，VM判断是否需要进行JIT优化是通过以下两种策略的一种：method-at-a-time（如果函数的执行超过了阈值，就进行JIT优化）和tracing (如果循环的执行超过了阈值，就进行JIT优化)。但是HHVM使用的是一种独特的策略，basic-block-at-a-time，这个策略和VMware的x86 hypervisor相似。使用这个策略与Facebook希望支持类型推导的闭包有关。

## Hack

上面提到了类型推导。事实上，Facebook推出了一个运行在HHVM上的PHP改良语言——Hack。Hack里加入了类型的支持：

<?hh
class MyClass {
  const int MyConst = 0;
  private string $x = '';
  public function increment(int $x): int {
    $y = $x + 1;
    return $y;
  }
}
加了类型之后，除了方便大型团队协作，避免编程中出现的错误之外，还有一个重要的原因就是能够让HHVM更好地优化性能。JIT优化最主要的方面就是根据类型来生成特定的指令，这样可以减少大量的指令和条件判断。而对于PHP这样的动态语言，要推断清楚类型是非常困难的，所以Hack就直接让程序员写上了。

## 兼容性

HHVM除了作为Hack的VM之外，还可以运行原生的PHP。兼容性测试表明，HHVM对PHP的兼容度已经达到98.58%了。由于HHVM使用了独特的JIT优化策略，因此Facebook自行开发了tracelet辅助库，这个库只支持x86 64bit系统，所以HHVM也只能在64位系统上使用——不过这个问题不大，现在的服务器硬件基本都支持64位了。需要考虑的是PHP扩展的问题。由于PHP语言包含非常之多的扩展，而Facebook的HHVM只实现了自家用到的扩展，所以可能有为HHVM重写PHP扩展的需要。好在相比为官方PHP实现写扩展，为HHVM写扩展比较容易，对性能要求不高的扩展可以使用纯PHP编写，然后编译到HHVM二进制文件中即可，详见HHVM wiki。还有一个要小心的问题就是HHVM是常驻内存的，所以如果某处PHP代码有内存泄露问题的话，可能拖慢整个HHVM服务的速度，甚至导致HHVM挂掉。


## **其它阅读**

- [HHVM(HipHop PHP)优化加速PHP代码:搭建提速五六倍的PHP服务器](http://blog.chinaunix.net/uid-28838369-id-3793006.html)

- [Facebook 的 PHP 性能与扩展性](http://dbanotes.net/arch/facebook_php.html)
