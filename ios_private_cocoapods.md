# iOS Private CocoaPods

私有Cocoapods管理项目，介绍：

>CocoaPods is a great way to manage dependencies (mostly open source code) in your iOS or OS X project.

>It implements a Gemfile like configuration file (called a Podfile) where you declare your dependencies and provides all the means to keep those libraries up to date as your project grows.

> I want to cover a less common use of CocoaPods: creating your own Pods repository for internal use.


## 1. 个人私有库

- [https://bitbucket.org/](https://bitbucket.org/tinkl/podspec)申请免费使用库
- 申请名为[https://bitbucket.org/你的用户名/podspec](https://bitbucket.org/你的用户名/podspec)


## 2.添加repository到cocoapods（Add the repository to CocoaPods）

` pod repo add MyPrivatePods git@bitbucket.org:你的用户名/podspec.git`

>这里注意的是：需要把当前本机cocoapods升级到最新版本.
执行：`gem update cocoapods`

## 3. 创建podspecs文件(Create your Podspecs)

1. `pod spec create MyLibrary` 注意：MyLibrary是私有库名称
2. 目录下将会生成一个`MyLibrary.podspec`文件
3. 执行`pod spec lint MyLibrary.podspec`
>Now you need to edit that Podspec to make it complete and lint. Once you do, you can lint it to make sure the project compiles, by running
pod spec lint MyLibrary.If everything passes, you are good to go! If not, fix the errors in the Podspec and make sure it lints.


这里需要注意：可能会出现`The version should be included in the Git tag.`错误，处理方式是：看来CocoaPods验证spec文件时会匹配git地址的tag和配置的spec文件中的version，如果二者不比配，CocoaPods判断版本有可能不一致，就会显示这样一句warning。将spec文件中的s.version改成和tag改成一样的数字之后，这个warning顺利地被解决了。

cocoapods源码里有发现：
```
if tag && !tag.to_s.include?(version)
            warning '[source] The version should be included in the Git tag.'
          end
```

正常运行会显示出：
![](http://tinkl.qiniudn.com/tinkl16.pic.jpg)

## 4. 添加MyLibrary.podspec到repository (Adding the Podspec to your repository)

The final step is adding the Podspec to your private specs repository. To do that, you run` pod repo push MyPrivatePods MyLibrary.podspec`

This step will lint the library again, and copy it to you local repo, and push that repo to git. If you don’t want it to push, as an extra measure, you can add the --local-only parameter1.


![](http://tinkl.qiniudn.com/tinkl17.pic.jpg)


这里有个处理是：MyPrivatePods 名称对应的是`~/.cocoapods/repos`下的MyPrivatePods目录
执行到这里私有库基本完成，接下来就是集成到项目里了。

##5.分享给其它团队(Sharing with teammates)
To share these specs with your teammates, they only need to add the new specs repository to CocoaPods by running` pod repo add teambestSpec git@bitbucket.org:你的用户名/podspec.git`

这样会在`~/.cocoapods/repos`目录下新出现teambestSpec目录

## 6.Conclusions
CocoaPods is an amazing tool. For some companies dealing with internal reusable libraries is a daily business. Using CocoaPods as depicted has several benefits:

- Your internal specs can use the dependency management of CocoaPods, just as any open source library.
- Your projects use a single way of dependency management, both for open source libraries and internal ones.

It’s straightforward to share an internal specs repository with teammates.



## 7.集成到项目里:
Podfile文件:
```
platform :ios, "7.0"

pod "TKPhoto"
```
然后依次执行`pod install --verbose`

![](http://tinkl.qiniudn.com/tinkl1.pic_hd.jpg)
![](http://tinkl.qiniudn.com/tinkl2.pic_hd.jpg)
![](http://tinkl.qiniudn.com/tinklED9DDAFB-4E38-4304-99C6-F46C6F463259.png)
![](http://tinkl.qiniudn.com/tinkl3.pic.jpg)


题外资料：

-  [http://guides.cocoapods.org/making/private-cocoapods.html](http://guides.cocoapods.org/making/private-cocoapods.html)
-  [http://guides.cocoapods.org/syntax/podfile.html#pod](http://guides.cocoapods.org/syntax/podfile.html#pod)
- [http://stackoverflow.com/questions/25759170/how-to-add-a-private-cocoapod-as-a-dependency-in-another-pod-podspec-file?rq=1](http://stackoverflow.com/questions/25759170/how-to-add-a-private-cocoapod-as-a-dependency-in-another-pod-podspec-file?rq=1)
