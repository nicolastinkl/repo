# arithmetic算法

- 统治世界的十大算法
- 算法学习资料



#`统治世界的十大算法`
 ###什么是算法？
>通俗而言，算法是一个定义明确的计算过程，可以一些值或一组值作为输入并产生一些值或一组值作为输出。因此算法就是将输入转为输出的一系列计算步骤。

###简而言之，算法就是可完成特定任务的一系列步骤，它应该具备三大特征
 1、有限

 2、指令明确

 3、有效

>Marcos Otero 推荐的十大算法


###1. 归并排序、快速排序及堆积排序
![](http://a.36krcnd.com/photo/2014/4517c4d946db8531277f333d6a7f6491.png)

最好的排序算法跟需求密切相关，很难评判。但是从使用上说，这三种的使用频率更高。

`归并排序`由冯•诺依曼于 1945 年发明。这是一种基于比较的排序算法，采用分而治之的办法解决问题，其阶是 O(n^2)。

`快速排序`可采用原地分割方法，也可采用分而治之算法。这不是一种稳定的排序算法，但对于基于 RAM（内存）的数组排序来说非常有效。

`堆排序`采用优先级队列来减少数据中的搜索时间。该算法也是原地算法，并非稳定排序。
这些排序算法相对于以前的冒泡排序算法等有了巨大改进，实际上我们今天的数据挖掘、人工智能、链接分析及包括 web 在内的大多数计算工具都要感谢它们。

###2、傅里叶变换与快速傅里叶变换
![](http://a.36krcnd.com/photo/2014/474cc7ff645f718306b75d8424dcd510.jpg)
我们的整个数字世界都使用这两个简单但非常强大的算法，其作用是将信号从时域转为频域或者反之。实际上，你看得到这篇文章得感谢这些算法。

互联网、你的 WiFi、智能手机、电话、计算机、路由器、卫星，几乎所有内置有计算机的东西都会以各种方式使用这两算法。如果不研究这些算法，你就拿不到电子、计算或通信方面的学位。

###3、迪杰斯特拉（Dijkstra）算法
![](http://a.36krcnd.com/photo/2014/21924f5958515dd7fdc7da1cd37e9639.jpeg)
`Dijkstra`是一种图谱搜索算法。许多问题都可以建模为图谱，然后利用 Dijkstra 寻找两个节点之间的最短路径。如果没有 Dijkstra 算法，互联网的运营效率必将大大降低。虽然今天我们已经有了更好的寻找最短路径的解决方案，但出于稳定性的要求，Dijkstra 算法仍然被很多系统使用。

###4、RSA算法
如果没有密码术和网络安全，互联网就不会像今天一样重要，因为电子商务和电子交易需要这些技术来确保交易安全。而RSA算法是最重要的密码学算法之一。该算法由同名公司的创始人（Ron Rivest、Adi Shamir 和 Leonard Adleman）开发，它让密码学普及到了千家万户并奠定了密码术的应用基础。RSA 要解决的问题既简单又复杂：如何在独立平台与最终用户之间共享公钥。其解决方案是加密。RSA 加密的基础是一个十分简单的数论事实：将两个大素数相乘十分容易，但是想要对其乘积进行因式分解却极其困难，因此可以将乘积公开作为加密密钥。但在分布式计算和量子计算机理论日趋成熟的今天，RSA 加密安全性受到了挑战。

###5、安全哈希算法（SHA）

这个实际上并不算是算法，而是由美国国家标准技术研究所开发的一系列密码杂凑函数。但是这系列函数是全世界运作的基石。应用商店，电子邮件、反病毒、浏览器等在使用SHA系列函数，SHA 函数可用来确定下载的东西是否自己想要的东西，还是说遭遇了中间人攻击或钓鱼攻击。

###6、整数因子分解

这是一个在计算领域使用频繁的数学算法。如果没有这一算法，密码术就会变得不安全得多。整数因子分解是用来将一个合数分解成一系列素因子的一系列步骤。整数因子分解可被视为是 FNP 问题（FNP 是难以解决的典型 NP 问题的扩展）。

许多密码协议均基于难以分解的大型合数或相关问题。比方说前面提到的 RSA 问题。如果有算法能够有效分解任意数字，那么就会使得基于 RSA 的公钥密码系统陷入不安全的境地。

而量子计算的诞生则令此问题的解决变得容易，从而也打开了一个全新的领域，可利用量子世界的属性来令系统更加安全。
###7、链接分析
![](http://a.36krcnd.com/photo/2014/bdd65b3a4b0fb9f9279e825be9c1fa80.jpg)
在互联网时代，不同实体间关系的分析至关重要。从搜索引擎和社交网络到营销分析工具，每个人都想找出互联网的真正结构。

链接分析无疑是公众对算法的最大困惑与迷思之一。其问题在于进行链接分析有不同的方式，而增加一些特征就会令每一算法略有不同（从而使得算法受到专利保护），但基本上这些算法都是类似的。

链接分析算法首先由 Gabriel Pinski 和 Francis Narin 在 1976 年发明。其背后的思路很简单，即把图谱以矩阵的形式表示，从而转为特征值问题，而特征值有助于了解图谱结构及每个节点的相对重要性。

Google 的 PageRank，Facebook 展示新闻源，Google+，Facebook 朋友推荐，LinkedIn 工作及联系人推荐，Netflix 与 Hulu 的电影推荐，YouTube 视频推荐等均使用了链接分析算法。虽然每个都有不同的目标和参数，但其背后的数学是一样的。

尽管 Google 似乎是利用此类算法的第一家公司，但是实际上百度创始人李彦宏在 Google 诞生 2 两年前做的搜索引擎“RankDex”已经利用这种思路来进行搜索排名了。

###8、比例积分微分算法

如果你用过飞机、汽车、微型服务或手机网络，如果你在工厂呆过或者见过机器人，那么你已经见识过这一PID算法的作用了。

该算法利用了控制回路机制来让期望输出信号与实际输出信号之间的错误降到最小。只要需要信号处理或需要电子系统来控制自动化的机械、水力或热力系统就要用到它。

因此可以说如果没有这一算法，人类的现代文明将不复存在。

###9、数据压缩算法

数据压缩算法无疑是非常重要的，因为几乎在所有的结构中都要用到。除了最明显的压缩文档以外，网页下载时也会压缩，视频游戏、视频、音乐、数据存储、云计算、数据库等等也都要使用压缩算法。可以说几乎所有应用都要使用压缩算法。压缩算法令系统更有效成本更低，但是要想确定哪一个最重要却很困难，因为应用不同，使用的压缩算法从 zip 到 mp3、JPEG 或 MPEG-2 各异。

###10、随机数生成算法

很多应用都需要随机数。像 interlink connection，密码系统、视频游戏、人工智能、优化、问题的初始条件，金融等都需要生成随机数。但实际上目前我们并没有“真正”的随机数生成器，尽管有一些伪随机数生成器也是非常有效的。

当然，十大算法也可能给有凑数之嫌，审视的角度不同对算法的重要性看法也会很不一样，如果你认为这一榜单有错漏的地方，不妨在评论中贡献你的意见。


#`算法学习资料`

- [结构之法 算法之道](http://blog.csdn.net/v_july_v/article/category/795430) 该博主学习结构和算法已经走火入魔
- [预览算法视频](http://top.jobbole.com/1322/)
- [内存回收算法视频](http://blog.jobbole.com/77280/)
