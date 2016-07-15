# Android汇编

1. apktool反编译出apk中的资源文件, 例如AndroidManifest.xml和res

    `java -jar apktool.jar d -d xxx.apk`

2. dex2jar反编译出apk中的class

 // dex2jar is deprecated, use the d2j-dex2jar

    `d2j-dex2jar xxx.apk`

3. jd-ui/jad反编译class为java源文件

 反编译工具还是jad最强, JD-GUI只是方便反编译/查看整个jar而已, 反编译出来的代码不全, 因此需要jd + jad(或者jadclipse eclipse插件使用更方便)配合才完美


参考
- [一个APK反编译利器Apktool](http://blog.sina.com.cn/s/blog_5752764e0100kv34.html)
- [Android程序反编译的方法](http://www.cnblogs.com/feisky/archive/2010/08/05/1793493.html)
- [如何反编译android程序](http://doandroid.info/%E5%A6%82%E4%BD%95%E5%8F%8D%E7%BC%96%E8%AF%91android%E7%A8%8B%E5%BA%8F/)
