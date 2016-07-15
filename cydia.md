破解软件的问题，其实不仅仅是iOS上，几乎所有平台上，无论是pc还是移动终端，都是顽疾。可能在中国这块神奇的国度，大家都习惯用盗版了，并不觉得这是个问题，个人是这么想，甚至某些盈利性质的公司也这么想，著名的智能手机门户网站91.com前不久就宣传自己平台上盗版全，不花钱。其实这种把盗版软件当成噱头的网站很多，当然还没出现过91这种义正言辞地去宣传用盗版是白富美，买正版是傻X的公司。大家都是做实诚样，把最新最受欢迎的盗版应用一一挂在首页来吸引用户。同步助手就是这种内有好货，不要错过的代表，并且在盗版圈子里，享有良好的口碑，号称同步在手，江山我有。

其实iOS破解软件的问题，既不起源于中国，现阶段也没有发扬光大到称霸的地位，目前属于老二，当然很有希望赶超。据统计，全球范围内，排名前五的破解app分享网站有以下几家：

1. Apptrack    代表软件：Crackulous，clutch
破解界的老大哥，出品了多款易用的破解软件，让人人都会破解，鼓励分享破解软件，破解app共享卡正以恐怖的速度扩充。

2. AppCake代表软件：CrackNShare
新秀，支持三种语言，中文（难得！）、英文、德文，前景很可观

3. KulApps 北美
4. iDownloads 俄国
5. iGUI 俄国

以上消息，对于依靠安装包收费的iOS开发来说，无疑是噩耗，其实由不少人也在网上号召支持正版，要求知识产权保护的力度加强。这些愿望或许终有一天会实现，但从技术的角度来分析问题的所在，给出有效的防御方案，无疑比愿望更快。

－－－－破解原理部分－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－


Appstore上的应用都采用了DRM（digital rights management）数字版权加密保护技术，直接的表现是A帐号购买的app，除A外的帐号无法使用，其实就是有了数字签名验证，而app的破解过程，实质也是去除数字签名的过程。去除过程包括两部分，如下所示：

条件一，设备越狱，获得root权限，去除掉设备上的签名检查，允许没有合法签名的程序在设备上运行

代表工具：AppSync（作者：Dissident ，Apptrack网站的核心人物）
（iOS 3.0 出现，不同的iOS版本，原理都不一样）
iOS 3.0后，MobileInstallation将可执行文件的签名校验委托给独立的二进制文件/usrlibexec/installd来处理,而AppSync 就是该执行文件的一个补丁，用来绕过签名校验。

iOS 4.0后，apple留了个后门（app给开发者预留的用于测试的沙盒环境），只要在/var/mobile/下建立tdmtanf目录，就可以绕过installd的签名校验，但该沙盒环境会造成没法进行IAP购买，无法在GameCenter查看游戏排名和加好友，特征是进入Game Center会出现SandBox字样。AppSync for iOS 4.0 +修复了这一问题。

iOS 5.0后，apple增加了新的安全机制来保护二进制文件，例如去掉二进制文件的符号表，给破解带来了难度。新版的AppSync for iOS 5.0+ 使用MobileSubstrate来hook libmis.dylib库的MISValidateSignatureAndCopyInfo函数来绕过签名验证



条件二，解密mach－o可执行文件
一般采用自购破解的方法，即先通过正常流程购买appstore 中的app，然后采用工具或手工的方式解密安装包中的mach－o可执行文件。
之所以要先获得正常的IPA的原因是mach－O文件是有DRM数字签名的，是加密过的，而解密的核心就是解密加密部分，而我们知道，当应用运行时，在内存中是处于解密状态的。所以首要步骤就是让应用先正常运行起来，而只有正常购买的应用才能达到这一目的，所以要先正常购买。

购买后，接着就是破解了。随着iOS设备cpu 的不同（arm 6 还是arm 7），mach－o文件格式的不同（thin binary 还是fat binary），应用是否对破解有防御措施（检测是否越狱，检测应用文件系统的变化），破解步骤也有所不同，但核心步骤如下：

第一步：获得cryptid，cryptoffset，cryptsize
cryptid为加密状态，0表示未加密，1表示解密；
cryptoffset未加密部分的偏移量，单位bytes
cryptsize加密段的大小，单位bytes
第二步：将cryptid修改为0
第三步：gdb导出解密部分
第四步：用第二步中的解密部分替换掉加密部分
第五步：签名
第六步：打包成IPA安装包

整个IPA破解历史上，代表性的工具如下：

代表工具：Crackulous（GUI工具）（来自Hackulous）

crackulous最初版本由SaladFork编写，是基于DecryptApp shell脚本的，后来crackulous的源码泄露，SaladFork放弃维护，由Docmorelli接手，创建了基于Clutch工具的最近版本。

代表工具：Clutch（命令行工具）（来自Hackulous）
由dissident编写，Clutch从发布到现在，是最快的破解工具。Clutch工具支持绕过ASLR（apple在iOS 4.3中加入ASLR机制）保护和支持Fat Binaries，基于icefire的icecrack工具，objective－c编写。

代表工具：PoedCrackMod（命令行工具）（来自Hackulous）
由Rastignac编写，基于poedCrack，是第一个支持破解fat binaries的工具。shell编写


代表工具：CrackTM（命令行工具）（来自Hackulous）
由MadHouse编写，最后版本为3.1.2，据说初版在破解速度上就快过poedCrack。shell编写


（以下是bash脚本工具的发展历史（脚本名（作者）），虽然目前都已废弃，但都是目前好用的ipa 破解工具的基础。
autop(Flox)——>xCrack(SaladFork)——>DecryptApp(uncon)——>Decrypt(FloydianSlip)——>poedCrack（poedgirl）——>CrackTM(MadHouse)


代表工具：CrackNShare （GUI工具）（来自appcake）
基于PoedCrackMod 和 CrackTM


我们可以通过分析这些工具的行为，原理及产生的结果来启发防御的方法。

像AppSync这种去掉设备签名检查的问题还是留给apple公司来解决（属于iOS系统层的安全），对于app开发则需要重点关注，app是如何被解密的（属于iOS应用层的安全）。

我们以PoedCrackMod和Clutch为例

一、PoedCrackMod分析（v2.5）
源码及详细的源码分析见：
http://danqingdani.blog.163.com/blog/static/18609419520129261354800/

通过分析源码，我们可以知道，整个破解过程，除去前期检测依赖工具是否存在（例如ldid，plutil，otool，gdb等），伪造特征文件，可以总结为以下几步：

第一步. 将fat binary切分为armv6，armv7部分（采用swap header技巧）
第二步：获得cryptid，cryptoffset，cryptsize
第三步. 将armv6部分的cryptid修改为0，gdb导出对应的armv6解密部分（对经过swap header处理的Mach－O文件进行操作，使其在arm 7设备上，强制运行arm 6部分），替换掉armv6加密部分，签名
第四步. 将armv7部分的cryptid修改为0，gdb导出对应的armv7解密部分（对原Mach－O文件进行操作)，替换掉armv7加密部分，签名
第五步.合并解密过的armv6，armv7
第六步.打包成ipa安装包

注明：第三步和第四步是破解的关键，破解是否成功的关键在于导出的解密部分是否正确完整。
由于binary fat格式的mach－o文件在arm 7设备上默认运行arm 7对应代码，当需要导出arm 6对应的解密部分时，要先经过swap header处理，使其在arm 7 设备上按arm 6运行。



二、clutch分析
对于最有效的clutch，由于只找到了clutch 1.0.1的源码（最新版本是1.2.4）。所以从ipa破解前后的区别来观察发生了什么。
使用BeyondCompare进行对比，发现有以下变动。

1. 正版的iTunesMetadata.plist被移除
该文件用来记录app的基本信息，例如购买者的appleID，app购买时间、app支持的设备体系结构，app的版本、app标识符

2.正版的SC_Info目录被移除
SC_Info目录包含appname.sinf和appname.supp两个文件。
（1）SINF为metadata文件
（2）SUPP为解密可执行文件的密钥

3.可执行文件发生的变动非常大,但最明显的事是cryptid的值发生了变化
leetekiMac-mini:xxx.app leedani$ otool -l appname | grep "cmd LC_ENCRYPTION_INFO" -A 4
          cmd LC_ENCRYPTION_INFO
      cmdsize 20
    cryptoff  8192
    cryptsize 6053888
    cryptid   0
--
          cmd LC_ENCRYPTION_INFO
      cmdsize 20
    cryptoff  8192
    cryptsize 5001216
    cryptid   0

iOS平台游戏安全之IPA破解 - danqingdani - 碳基体

iTunesMetadata.plist 与 SC_Info目录的移除只是为了避免泄露正版购买者的一些基本信息，是否去除不影响ipa的正常安装运行。

－－－－破解防御部分－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
在IPA防御方面，目前没有预防破解的好办法，但可以做事后检测，使得破解IPA无法正常运行以达到防御作用。
而该如何做事后检测呢，最直接的检测方法是将破解前后文件系统的变化作为特征值来检测。

通过分析PoedCrackMod源码，会发现根据破解前后文件时间戳的变化，或文件内容的变化为特征来判断是不可靠的，因为这些特征都可以伪造。如下所示，摘自于PoedCrackMod脚本


1.Info.plist
增加SignerIdentity,(目前主流的MinimumOSVersion版本为3.0，版本3.0之前的需要伪造SignerIdentity）
 plutil -key 'SignerIdentity' -value 'Apple iPhone OS Application Signing' "$WorkDir/$AppName/Info.plist" 2>&1> /dev/null

伪造Info.plist文件时间戳
touch -r "$AppPath/$AppName/Info.plist" "$WorkDir/$AppName/Info.plist"

2.iTunesMetadata.plist
伪造iTunesMetadata.plist文件
plutil -xml "$WorkDir/iTunesMetadataSource.plist" 2>&1> /dev/null

echo -e "\t<key>appleId</key>" >> "$WorkDir/iTunesMetadata.plist" #伪造AppleID

echo -e "\t<string>ChatMauve@apple.com</string>" >> "$WorkDir/iTunesMetadata.plist"

echo -e "\t<key>purchaseDate</key>" >> "$WorkDir/iTunesMetadata.plist" #伪造购买时间

echo -e "\t<date>2010-08-08T08:08:08Z</date>" >> "$WorkDir/iTunesMetadata.plist"



伪造iTunesMetadata.plist文件的时间戳

touch -r "$AppPath/$AppName/Info.plist" "$WorkDir/iTunesMetadata.plist"



3.mach－O文件

Lamerpatcher方法中，靠替换mach－O文件中用于检测的特征字符串来绕过检测

（题外话：设备是否越狱也可以通过检测文件系统的变化来判断，例如常见越狱文件，例如/Application/Cydia.app

/Library/MobileSubstrate/MobileSubstrate.dylibd)

            sed --in-place=.BCK \

                -e 's=/Cydia\.app=/Czdjb\.bpp=g' \

                -e 's=/private/var/lib/apt=/prjvbtf/vbr/ljb/bpt=g' \

                -e 's=/Applicat\d0\d0\d0ions/dele\d0\d0\d0teme\.txt=/Bppljcbt\d0\d0\d0jpns/dflf\d0\d0\d0tfmf\.txt=g' \

                -e 's=/Appl\d0\d0\d0ications/C\d0\d0ydi\d0a\.app=/Bppl\d0\d0\d0jcbtjpns/C\d0\d0zdj\d0b\.bpp=g' \

                -e 's=ations/Cy\d0\d0\d0/Applic\d0pp\d0\d0dia.a=btjpns/Cz\d0\d0\d0/Bppljc\d0pp\d0\d0djb.b=g' \

                -e 's=ate/va\d0\d0/priv\d0\d0\d0pt/\d0b/a\d0r/li=btf/vb\d0\d0/prjv\d0\d0\d0pt/\d0b/b\d0r/lj=g' \

                -e 's=pinchmedia\.com=pjnchmfdjb\.cpm=g' \

                -e 's=admob\.com=bdmpb\.cpm=g' \

                -e 's=doubleclick\.net=dpvblfcljck\.nft=g' \

                -e 's=googlesyndication\.com=gppglfszndjcbtjpn\.cpm=g' \

                -e 's=flurry\.com=flvrrz\.cpm=g' \

                -e 's=qwapi\.com=qwbpj\.cpm=g' \

                -e 's=mobclix\.com=mpbcljx\.cpm=g' \

                -e 's=http://ad\.=http://bd/=g' \

                -e 's=http://ads\.=http://bds/=g' \

                -e 's=http://ads2\.=http://bds2/=g' \

                -e 's=adwhirl\.com=bdwhjrl\.cpm=g' \

                -e 's=vdopia\.com=vdppjb\.cpm=g' \

                "$WorkDir/$AppName/$AppExecCur"

            #    "/Applications/Icy\.app"

            #    "/Applications/SBSettings\.app"

            #    "/Library/MobileSubstrate"

            #    "%si %sg %sn %se %sr %sI %sd %st %sy"

            #    "Sig nerId%@%@     ent ity "

            #    "Si  gne rIde    ntity"

伪造Mach－O文件时间戳

touch -r "$AppPath/$AppName/$AppExec" "$WorkDir/$AppName/$AppExec"


 所以最可靠的方法是根据cryptid的值来确定，为0便是破解版。当检测出破解版本时注意，为了避免逆向去除检测函数，需要多处做检测。同时检测函数要做加密处理，例如函数名加密，并要在多处进行检测。

而根据特征值来检测破解的方法也不是完全没用的，可以将特征值加密成无意义的字符串，最起码Lamerpatcher方法就无效了。同样，检测函数需要做加密处理，并要在多处进行检测。

看了破解ipa的原理，你会发现，所有的工具和方法都必须运行在越狱机上，因此将安全问题托付给苹果，幻想他可以将iOS系统做得无法越狱，他提供的一切安全措施都能生效（例如安全沙箱，代码签名，加密，ASLR，non executable memory，stack smashing protection）。这是不可能的，漏洞挖掘大牛门也不吃吃素的，自己的应用还是由自己来守护。

目前iOS平台游戏安全系列已完成3篇
《iOS平台游戏安全之八门神器内存修改，IAP Free游戏内购破解的防御》
《iOS平台游戏安全之存档修改与防御》
《iOS平台游戏安全之IPA破解原理与防御》

计划再写两篇
《iOS平台游戏安全之逆向》
《iOS平台游戏安全之通信协议安全》
来完成本系列。
-----------------------------------------------------------补充部分-------------------------------------------------
感谢cdteam的huangzhuliang，指出了我文章中错误的地方

补充：
iOS 应用软件保护的问题，除了上文提到的在越狱机上安装破解软件外。还有一种，针对非越狱机的方法。
以PP助手和快用苹果助手为例，不越狱，也可以安装app store里的“正版”应用。
我们可以通过逆向PP助手的dll文件，发现其原理是将正版购买相关信息同步到设备上，让其通过签名检查。

具体原理待分析

欢迎交流！


参考：
http://hackulo.us/wiki/

from [博客](http://danqingdani.blog.163.com/blog/static/186094195201292273453797/?COLLCC=876713730&)
