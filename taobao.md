# taobao 客户端架构


# 前言：
 #### WindVane

-  衔接 Android/iOS 的导航交互及动画

-  URL总线（对接Native UI总线）

-  JS Bridge：JS <-> Native（+ 性能及安全性增强）

-  缓存 & 预缓存（兼容Cache-Control + AppCache）

-  数据采集、本地/网络性能监控

-  安全加固、审核、隔离，人机识别

-  增强的网络层（SPDY、DNS旁路解析等）


### 核心概念: *归一、轻量、透明、延展、敏捷*



# 1. 归一

> 一切皆组件：告别插件，拥抱组件（bundle）

![](http://lijuren.qiniudn.com/product/lijuren/img/51.pic.jpg?attname=&e=1413431634&token=v0XCq4HVKSEf3J_GO6tr4uM1utAnXNKgxmfMJhb3:pjDcfQw3DZ2F7turae_nygDElvQ)


# 2. 轻量
> 微内核：只负责最基础的管理职能（~150K）

1 启动流程
-   容器初始化
-   类加载（首次启动还需dexopt）
-   核心中间件初始化（可异步）
-   启动入口Bundle

2  组件管理：只负责『添加』、『删除』、『替换』（支持批量事务化）
-   与上层的动态部署组件配合完成组件的在线升级


# 3. 透明
> 透明的容器（Android）：Bundle 即 App，容器亦 OS
  生命周期管理：Bundle 开发与 App 开发的无差异化

![](http://lijuren.qiniudn.com/product/lijuren/img/3.pic.jpg?attname=&e=1413432218&token=v0XCq4HVKSEf3J_GO6tr4uM1utAnXNKgxmfMJhb3:-mv4Gg_75iOBvdPG30lezXoKw2o)


> 总线

-   #### UI总线：以跨平台统一的URL作为寻址方式（Web、Android、iOS）
 - item.taobao.com/… → DetailActivity / TBTradeDetailViewController
 - 自动降级：没有 Bundle 承载的 URL，将自动以 Web 容器加载
 - Android：去中心化 + URL Hook / iOS：中心分发（支持Hook）

-   #### 服务总线：基于接口的服务调用（Android：兼容原生AIDL）
 -  [Android] 轻量化的服务启动（向下兼容原生的Service Binding）
 -  [Android] 支持跨进程、跨App的服务部署

-   #### 消息总线：基于OS原生消息机制（Broadcast / NSNotification）
 -  [Android] 进程内使用轻量级消息通道


# 4. 延展

-  #### 标准化（Android）
 - Bundle的交付产物：AAR，构建产物：AWB（相当于APK剔除了共享依赖）
 -  Bundle间可跨进程，甚至跨App对接

-  #### 灵活性
 -  Bundle的自由组装（手机淘宝、淘宝生活、码上淘）
 -  Bundle对容器无依赖

-   ####  适应性：从微型App到巨型App，按需采用的容器能力
 -  容器完全独立的三大职能：启动加载、生命周期管理、组件管理
 -  [Android] 三大总线均为OS能力的封装，具有极佳的互操作性

-   #### 从巨型App时代的臃肿，回归田园App时代的轻盈
    - 开发：像微型App一样便捷的开发调试（不依赖容器）
    - 开发环境的Bundle构建速度：3~10秒
-  集成：极简的集成模型（直接集成Bundle的构建产物AWB）
-  测试：『重』bundle测试，『轻』集成测试
-  部署：自动化的 Bundle 在线部署




# 5. 敏捷

> 『Move fast and break things』

- via Hot Patch
 - 线上严重问题的快速修复（小时级的响应时间）
 -  可对 Android Framework 代码打补丁
-  AOP 编码形式
 -  Before / After / Replace 某个方法
 -  支撑了『Project Nish』（目前尚未公开的神秘项目）
 -  暂时只支持 Dalvik，即将适配 ART 和 阿里云OS



# 回顾2010年-2014年
> 痛点：

- 协同方式
 - 迭代依赖
 - 分支管理
 - 合并依赖关系过于复杂！
- 调试自测效率
 - 模块依赖下的不稳定因素
 - 业务多，回归成本大
 - 测试资源严重不足！其他模块引起的不稳定性因素
- 发布的灵活性
 - 版本发布无法快速响应
 - 线上已发布版本稳定性
 - 灰度以及线上版本crash难以修复




> 解决思路:围绕着开发效率和性能稳定性等一系列问题


 - 工程拆分
    - 支持多团队并行开发
        - 并行开发(*业务解耦*)
        - 独立调试(*集成之前，在稳定环境下测试*)
        - 易于集成(*修改配置完成集成*)

 - 架构重构
    - 重新梳理容器和总线规则
        - 迭代开发，并行开发能力差
        - 耦合严重，核心功能（URL导航）复杂
        - 试错成本过高，增加减少业务带来的成本
        - 快速迭代下的稳定性问题
 - 配套工具
    - 使用有力工具增加开发效率

        - 工程拆分遇到的问题
            - 频繁的更换spec
            - 源码引入造成的pod update缓慢等原因
            - 开发阶段集成阶段等问题
        - 工具解决
            - 摩天轮自动打包平台（自动生成spec，framework引入）
            - 开发-集成-灰度，多阶段管理
        - 其他工具解决的问题
            - 核心链路性能监控平台
            - Crash分析平台


> 分而治之 一切皆组件

![](http://lijuren.qiniudn.com/product/lijuren/img/4.pic_hd.jpg?attname=&e=1413434787&token=v0XCq4HVKSEf3J_GO6tr4uM1utAnXNKgxmfMJhb3:doCsWB69uJuPiC_Y07wq5cTsutw)

![](http://lijuren.qiniudn.com/product/lijuren/img/6.pic_hd.jpg?attname=&e=1413434962&token=v0XCq4HVKSEf3J_GO6tr4uM1utAnXNKgxmfMJhb3:7wDX44tvwe9DxGOsqGhCNkHEfZQ)

# 未来
 - Bundle重组，互通有无
    - 业务复用，减少人力
    - 基础复用，做深做精
    - 敏捷开发，快速试错

 - 开发透明，实时发版
    - 动静结合，开发透明
    - 动态部署，渠道推送




