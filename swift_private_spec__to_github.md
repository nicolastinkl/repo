# swift Private spec  to github
> 创建TKAlamofire.podspec文件

1. `pod spec create https://github.com/nicolastinkl/TKAlamofire`

2. `编辑TKAlamofire.podspec文件`

```
#
#  Be sure to run `pod spec lint TKAlamofire.podspec' to ensure this is a
#  valid spec and to remove all comments including this before submitting the spec.
#
#  To learn more about Podspec attributes see http://docs.cocoapods.org/specification.html
#  To see working Podspecs in the CocoaPods repo see https://github.com/CocoaPods/Specs/
#

Pod::Spec.new do |s|

  # ―――  Spec Metadata  ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  These will help people to find your library, and whilst it
  #  can feel like a chore to fill in it's definitely to your advantage. The
  #  summary should be tweet-length, and the description more in depth.
  #

  s.name         = "TKAlamofire"

  s.version      = "1.1.4"

  s.summary      = "Elegant HTTP Networking in Swift"

  s.description  = "Elegant HTTP Networking in Swift by tinkl"

  s.homepage     = "https://github.com/nicolastinkl/TKAlamofire"

  s.license      = "MIT"

  s.author       = { "tinkl" => "nicolastinkl@gmail.com" }

  #  s.platform     = :ios, "8.0"

  #  When using multiple platforms


  s.ios.deployment_target = '8.0'
  s.osx.deployment_target = '10.9'

  s.source       = { :git => "https://github.com/nicolastinkl/TKAlamofire.git", :tag => s.version }

  s.source_files = 'Source/*.swift'

  s.requires_arc = true

end


```

3  `pod spec lint TKAlamofire.podspec`

4  `pod trunk push TKAlamofire.podspec`



> 备注：trunk服务

```
发布CocoaPod
CocoaPods 0.33中加入了Trunk服务。

虽然一开始使用GitHub Pull Requests来整理所有公共pods效果很好。但是，随着Pod数量的增加，这个工作对于spec维护人员Keith Smiley来说变得十分繁杂。甚至一些没有通过$ pod lint的spec也被提交上来，造成repo无法build。

CocoaPods Trunk服务的引入，解决了很多类似的问题。CocoaPods作为一个集中式的服务，使得分析和统计平台数据变得十分方便。

要想使用Trunk服务，首先你需要注册自己的电脑。这很简单，只要你指明你的邮箱地址（spec文件中的）和名称即可。

$ pod trunk register ####@nshipster.com "Mattt Thompson"

至此，你就可以通过以下命令来方便地发布和升级你的Pod！

$ pod trunk push NAME.podspec

```

##  gmail邮件确认trunk服务

```
Hi nicolas tinkl,

Please confirm your registration with CocoaPods by clicking the following link:

https://trunk.cocoapods.org/sessions/verify/5f332423935ab
If you did not request this you do not need to take any further action.

Kind regards, the CocoaPods team
```


> 私有项目用法

```
pod 'XXXXXXX', :git => 'https://github.com/nicolastinkl/TKAlamofire'

```


## 版本要求
```
$ gem -v
2.2.2

$ pod --version
0.36.0

```


##结果

![](http://tinkl.qiniudn.com/tinklUpload_9.pic.jpg)
```
$ pod search tkal


-> TKAlamofire (1.1.4)
   Elegant HTTP Networking in Swift
   pod 'TKAlamofire', '~> 1.1.4'
   - Homepage: https://github.com/nicolastinkl/TKAlamofire
   - Source:   https://github.com/nicolastinkl/TKAlamofire.git
   - Versions: 1.1.4, 1.0.2 [master repo]

```



##  使用
```
$ cat Podfile

platform :ios, "8.0"

# inhibit_all_warnings!

pod 'TKAlamofire', '~> 1.1.4'

use_frameworks!

```
![](http://tinkl.qiniudn.com/tinklUpload_6.pic_hd.jpg)

```
Error: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/libtool: unknown option character `X' in: -Xlinker

Solve:
update your cocoapods
update your gem

```
