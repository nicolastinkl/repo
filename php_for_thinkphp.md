# php for thinkphp

[ThinkPHP](https://github.com/liu21st)
项目结构:

```
Common	项目公共文件目录，一般放置项目的公共函数
Conf	项目配置目录，项目所有的配置文件都放在这里
Lang	项目语言包目录（可选 如果不需要多语言支持 可删除）
Lib	项目类库目录，通常包括Action和Model子目录
Tpl	项目模板目录，支持模板主题
Runtime	项目运行时目录，包括Cache（模板缓存）、Temp（数据缓存）、Data（数据目录）和Logs（日志文件）子目录，如果存在分组的话，则首先是分组目录。


ThinkPHP	系统目录（下面的目录结构同上面的系统目录）
Public	网站公共资源目录（存放网站的Css、Js和图片等资源）
Uploads	网站上传目录（用户上传的统一目录）
Home	项目目录（下面的目录结构同上面的应用目录）
Admin	后台管理项目目录
```



Extend目录为系统扩展目录（核心版不含任何扩展），子目录结构为：
```
|-Action	控制器扩展
|-Behavior	行为扩展
|-Driver	驱动扩展
|  ├Driver/Cache		缓存驱动
|  ├Driver/Db		数据库驱动
|  ├Driver/Session	SESSION驱动
|  ├Driver/TagLib	标签库驱动
|  ├Driver/Template	模板引擎驱动
|
|-Engine	引擎扩展
|-Function	函数扩展
|-Library	类库扩展
|  ├ORG	ORG类库包
|  ├COM	COM类库包
|
|-Mode	模式扩展
|-Model	模型扩展
|-Tool	其他扩展或工具
|-Vendor	第三方类库目录
```


demo : [https://github.com/liu21st/examples](https://github.com/liu21st/examples)

扩展中心：[https://github.com/liu21st/extend](https://github.com/liu21st/extend)




-----
## 简介

ThinkPHP 是一个免费开源的，快速、简单的面向对象的 轻量级PHP开发框架 ，创立于2006年初，遵循Apache2开源协议发布，是为了敏捷WEB应用开发和简化企业应用开发而诞生的。ThinkPHP从诞生以来一直秉承简洁实用的设计原则，在保持出色的性能和至简的代码的同时，也注重易用性。并且拥有众多的原创功能和特性，在社区团队的积极参与下，在易用性、扩展性和性能方面不断优化和改进，已经成长为国内最领先和最具影响力的WEB应用开发框架，众多的典型案例确保可以稳定用于商业以及门户级的开发。

## 全面的WEB开发特性支持

最新的ThinkPHP为WEB应用开发提供了强有力的支持，这些支持包括：

*  MVC支持-基于多层模型（M）、视图（V）、控制器（C）的设计模式
*  ORM支持-提供了全功能和高性能的ORM支持，支持大部分数据库
*  模板引擎支持-内置了高性能的基于标签库和XML标签的编译型模板引擎
*  RESTFul支持-通过REST控制器扩展提供了RESTFul支持，为你打造全新的URL设计和访问体验
*  云平台支持-提供了对新浪SAE平台和百度BAE平台的强力支持，具备“横跨性”和“平滑性”，支持本地化开发和调试以及部署切换，让你轻松过渡，打造全新的开发体验。
*  CLI支持-支持基于命令行的应用开发
*  RPC支持-提供包括PHPRpc、HProse、jsonRPC和Yar在内远程调用解决方案
*  MongoDb支持-提供NoSQL的支持
*  缓存支持-提供了包括文件、数据库、Memcache、Xcache、Redis等多种类型的缓存支持

## 大道至简的开发理念

ThinkPHP从诞生以来一直秉承大道至简的开发理念，无论从底层实现还是应用开发，我们都倡导用最少的代码完成相同的功能，正是由于对简单的执着和代码的修炼，让我们长期保持出色的性能和极速的开发体验。在主流PHP开发框架的评测数据中表现卓越，简单和快速开发是我们不变的宗旨。

## 安全性

框架在系统层面提供了众多的安全特性，确保你的网站和产品安全无忧。这些特性包括：

*  XSS安全防护
*  表单自动验证
*  强制数据类型转换
*  输入数据过滤
*  表单令牌验证
*  防SQL注入
*  图像上传检测

## 商业友好的开源协议

ThinkPHP遵循Apache2开源协议发布。Apache Licence是著名的非盈利开源组织Apache采用的协议。该协议和BSD类似，鼓励代码共享和尊重原作者的著作权，同样允许代码修改，再作为开源或商业软件发布。


