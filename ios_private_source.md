# iOS Private source
个人Cocoapods资源用法：
1. `git clone git@bitbucket.org:XXXXX/podspec.git`
2. `pod repo add tkpodspec git@bitbucket.org:XXXXX/podspec.git`
3. 在项目Podfile文件里引用我相关的项目名称,如:
    ```
     # 私有库
     pod "TKPhoto",:head                #image基本工具类和部分全局函数
    ```
4. `pod install --verbose` and `pod update`


`例如：`
[https://github.com/nicolastinkl/TKCapture/blob/master/Podfile](https://github.com/nicolastinkl/TKCapture/blob/master/Podfile)
