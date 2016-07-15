# php 结构设计


 **通过对于MVC设计思想进行的拆解封装，来实现清晰，有效，一致性的程序设计思想。**

一个程序从逻辑结构上可看做是模型(Model),视图(View)和控制Controller)三个逻辑块。

在JAVA中Model层实现系统中的业务逻辑，通常可以用JavaBean或EJB来实现。 View层用于与用户的交互，通常用JSP来实现。Controller层是Model与View之间沟通的桥梁，通常是router servlet向应用端的扩展。
可 PHP 没有这样清晰的划分，所以需要将设计思想揉入到程序代码中去。

# 1. 程序代码类封装

> 对于一个应用需求（ex: similar相似商品页)，将需求先分为3个文件：similar.controller.php;similar.model.php.similar.view.php
通过名字我们也可以看出各文件中的代码用途，剩下就是要给各个文件中的类进行命名。
因为是这对一个应用的不同操作类，所以需要给各个类加不同的类目后缀，

例如similar.controller.php
```
//controller 控制程序类  完成此类中的方法是程序各页面流程，每一个方法对应一个同步请求页面（以配合页面的调用规则）
class similar{
//相似商品页的展现
functio do_opensearch($_param){
//请求参数过滤
//请求商品数据
//根据呈现规则处理商品数据
//渲染商品数据获取呈现html
//展示呈现html
}
}
```
对应的请求就是

http://s.xxx.com/similar/opensearch/

similar.model.php
```
//model 数据操作程序类  完成对此应用的数据请求封装，返回结果的结构解析处理
class similarMODEL{
}
```
similar.view.php

```
//model 呈现操作程序类  完成对此应用的数据呈现封装，返回给已经处理好的
class similarVIEW{
    function view_getDetail($_goodDataArr){
        return $html;
    }
}
```
# 2. 类调用

我们很清晰的将各个逻辑将不同代码封装在了不同文件和类中了。如何灵活的调用呢？PHP是一个解释语言，没有像JAVA和C一个的编译过程，无法享受编译语言，在预编译过程中的自动连接功能。
其实现在PHP解释器也已经意识到这个问题，并在5.3.1以后提出了autoload方法来解决，当解释器遇到一个非声明类或者函数时会自动运行autoload去再加载一次，如果还没有才报错。
详情参见 http://php.net/manual/en/language.oop5.autoload.php

但这样还是一个被动解决方案。下面提供一个主动动态加载方案。加入资源系统调动类
在讲解这个类的使用之前先看一下我们之前加载并使用一个类文件的过程：
文件:similar.controller.php
```
require_once PATH."similar.model.php";
$sModelObj = new similarMODEL;
$goodsData = $sModelObj->getGoodsInfoByNid($nid);```

这是在过程中调用，如果在controller类内调用的话就需要写成这样，
```
require_once PATH."similar.model.php";
class similar{
public $_modelObj;
function __construct(){
$this->_modelObj = new similarMODEL;
}
//相似商品页的展现
functio do_opensearch($_param){
//请求商品数据
$goodsInfoArr = $this->_modelObj->getGoodsInfoByNid($nid);
}
}//end class```

如果controller类需要使用另一个MODEL，例如forest,ha3什么的就要声明多个类内元素变量。如果利用PHP 的变量传导特性，建立一个资源调度类，来统一加载和调度需要的类并声明，使用起来就会方便很多。下面我们来详细的分解下这个资源调度类的设计，

```
/**
* 加载指定类型的类程序
**/
class Box {
//声明一个进程内资源对象池
public static $_modelObjArr;
//获取一个资源对象
public static function getObj($_appName,$_typeStr='class') {

if($_typeStr=='class'){
$className = $_appName;
}else{
$className = $_appName.strtoupper($_typeStr);
}
//资源对象已创建 直接返回使用
if( isset(self::$_modelObjArr[$className]) && is_object(self::$_modelObjArr[$className]) ){
return self::$_modelObjArr[$className;
}
//加载文件资源类文件
$file = dirname(__FILE__)."{$_appName}/{$_appName}.{$_typeStr}.php";
if( file_exists($file) ){
require_once $file;
if( class_exists($className) ){
return self::_createObj($className);
}else{
$errStr =  "no class {$className} in file {$file}"; //类名错误
}
} else {
$errStr = "no class file {$file}"; //类文件错误
}
self::_showErr($errStr);
}
//创建资源对象
public static function _createObj($_className){
if( isset(self::$_modelObjArr[$_className]) && is_object(self::$_modelObjArr[$_className]) ){
return self::$_modelObjArr[$_className];
}else{
self::$_modelObjArr[$_className] = new $_className();
return self::$_modelObjArr[$_className];
}
}
//错误提示
public static function _showErr($_errTypeStr=''){
echo $_errTypeStr;  exit;
//errorlog($_errTypeStr);
}
}//end class```

改用Box资源调度类，加载类程序
文件:similar.controller.php
```
class similar{

function __construct(){
}
//相似商品页的展现
functio do_opensearch($_param){
//请求商品数据
$goodsInfoArr = Box::getObj('similar','model')->getGoodsInfoByNid($nid);
$html = Box::getObj('similar','view')->view_getDetail($goodsInfoArr);
//其他已封装好的数据逻辑
$hotHtml = $this->_getHotHtml();
}
function do_search($_param){
$goodsInfoArr = Box::getObj('similar','model')->getGoodsInfoByNid($nid);
$html = Box::getObj('similar','view')->view_getDetail($goodsInfoArr);
}
function _getHotHtml(){
$hotInfoArr = Box::getObj('similar','model')->getGoodsInfoByNid($nid);
$html = Box::getObj('similar','view')->view_getDetail($goodsInfoArr);
return $hotInfoArr;
}
}//end class```


这样在一个响应进程内 “similar.model.php”; 文件只会被加载一次，similarMODEL 也只会被创建一次，
随使随调用。不用提前声明，不用考虑重复加载，重复创建。

Box解决了类与类间资源的调用和创建。

那如何让一个controller类文件中的function 运行呢？我们需要一个请求调度类 Bin。

之前我们的请求响应都市通过websearch的io model来完成。吧请求的path映射到实际文件中。
例如:
xxxx.xxx.com/similar/opensearch/index.php 或者是 xxx.xxx.com/similar/opensearch.php 这样的文件,websearch将host替换成实际文件系统的path。

这样外部程序可以各种扫描我们的目录，一旦疏忽就会把一些常用方法暴露出来，ex:xxxx.xxx.com/tools/info.php 什么的，有风险隐患。

当然加一些过滤条件是可以屏蔽这样的问题，但会增减webserver的逻辑，一旦有改动就会改动webserver的conf。麻烦。索性我们就webserver踏踏实实的做好io和http打包解包，

让app server来过滤和灵活配置请求调用。再webserver中将所有的访问都映射(rewitre)到host/dispatch.php，由Bin类来实现调度和分配。实现应用逻辑单一入口调用结构。

dispatch.php完成一个平台分配，性能监控，配置加载，应用启动，分类型输入(同步，异步，PC，mobile)，等平台级功能。

Bin类就需要如下功能: 解析请求，将webserver传递的环境变量传递到响应的实际处理应用来完成分解，提出similar 应用名称，opensearch 方法名称，

还有GET,POST请求参数，组装成请求参数数组，

```
$this->_paramArr = explode( '/', trim(strtok( urldecode($_SERVER["REQUEST_URI"]),'?'),'/'));
$this->_paramArr = array_merge($this->_paramArr,$_GET);
这样$this->_paramArr[0]就是appname应用名称
这样$this->_paramArr[1]就是function方法名称
```

平台流程就可以装载appname.class.php 文件并执行 appname->do_function($this->_paramArr);方法。
这样也支持/similar/opensearch/sort/这样的传参,sort是$this->_paramArr[2]的一个参数

如果$this->_paramArr[0]等于ajax也就是对于的这是一个js发起的异步请求，

要装载的就是appname.ajax.php,并执行appnameAJAX->ajax_function();

将同步和异步请求分文件。减少加载大文件对内存的占用。

同时也可以将可访问目录跟应用目录物理上分类，让扫描程序无用。

例如:

/home/website/host/ 这个目录只放index.php一个文件，和css,js,images一些没有加入cdn的呈现渲染文件。将webserver的docmentroot 或者laction 设置在这个目录
/home/website/app/ 这个目录来存放编写的类程序文件，配置，由/host/index.php程序加载app目录下的类程序文件

一个应用好办，一堆应用我们怎么办呢？下面我们来说说多应用的目录结构。


# 3.目录结构

PHP的设计理念这是少又少，连个包的概念都没有。只好我们自己用目录来包装包概念。从访问结构，开发结构，部署结构上来分别介绍目录结构的作用.

将访问目录host,和应用程序目录app可以看成是一个website，这个site下面的应用可以这样的

### 3.1 访问结构
可以这样建立
```
/website/host

/website/app/similaer

/website/app/cmp

/website/app/srp

/website/app/…..
```
假如管理工具也可以再website下同另起一个目录
```
/website/admin/firebox

/website/admin/seoAny
```
> 这样，所有的请求都会到/website/host/index.php由index.php在根据请求规则，加载响应的逻辑程序。

> 跟/webiste/host并行的有一个system目录,/website/system/,将box.class.php,bin.class.php,base.class.php等平台文件存放其中。
一个响应的执行流程就是

> webserver query ->[ /website/host/index.php include /website/system/box,bin,base （应用架构）] 平台-> [ /website/app/ (应用流程) | /website/admin/]页面

### 3.2 开发结构
在开发和维护过程中，会有一些同样地php库文件，如curl通信,xml解析,log,timer,template(appview),等自己开发的类程序，也有像smarty,big2gb等第三方类库。
各个项目都通用，比价，主搜，我的一淘，那就可以统一建立一个PHPLIBS目录与website平级
```
/website/host/

/website/app/

/website/system/

/PHPlibs/etao/ 自己的类库

/PHPlibs/other/ 第三方类库
```
每一个website一个svn
PHPlibs/独立一个svn 专人维护，多快好省精深专

将template文件从website/中分离出来建立一个
webtemplate/目录 跟ued同学共享一个svn, 让ued同学按前段template类的规则书写模板文件。按应用分目录存储。
如:
```
webtemplate/srp
webtemplate/cmp
webtemplate/opensearch
webtemplate/frame 统一呈现模板，页头尾
webtemplate/…..
website/app中的程序通过template类方法来调用以上模板文件。```

同时在开发和维护过程中也会遇到需要shell来执行的php script ，如定时生成公共页头尾，定时取mysql库中的epid数据等。或者临时做一个算法的程序。

**那我们就可以在**

/website/app/的相关应用中加入app.sh.php程序文件，
如
/website/app/similar/similar.sh.php 这个文件的类程序similarSH是通过
/website/script/run.php 来执行的。

script/run.php是一个shell端的控制器响应CLI请求，host/index.php是一个web端的控制器响应CGI请求
还可以在script/run.php 外包装一个debug.bat的文件，可已经在dos,或者包装一个debug.sh 程序再shell下直接交互调试。

**具体参考代码**

还有一些程序处理文件，如由vm文件转换成的php模板的公共页头尾文件，会有一个跟website平行的webdata文件，
```
/website/….
/webdata/pageframe等目录中，在app中直接调用
```


### 3.3 部署结构

通过上线脚本分别checkout

```
/website/
/PHPlibs/
/webtemplate/ svn资源库
```

然后打包rpm，推送上线。

单独上线，也是使用上线脚本配置检查出相应文件，推送到前段生产机。

# 4. 代码规范

关于代码格式规范，看之前大家也都积极的讨论了很久了。

代码格式规范，我们在IDE中配置一下，自动使用，并通过不断的review和提醒中培养好的书写易读代码的习惯就好了。

代码设计规范，是需要根据架构指定，并坚持执行的。

下面谈到代码设计都是基于刚刚谈到的，资源调用，请求分配来制定的。

### 4.1 关于目录
```
//应用程序
/website/app/….
//对外访问目录
/website/host/index.php
/website/host/css
/website/host/js
/website/host/images
//平台系统文件
/website/host/images
//调试，维护脚本
/website/script/
//模板库 对应/website/app/下的目录
/webtemplate/….
//程序类库
/PHPlibs/
//数据文件目录
/webdata
```
没什么好说的，大家体会。

### 4.2 关于文件

文件的命名规范主要是指定内部调用，和请求调用
```
website/app/appname/appname.apptype.php
apptype: class,model,view,ajax,api,sh ,….
调用:
Box::getObj(appname,apptype)->function();

appname.class.php文件为controller文件，类中的方法做同步请求响应。
appname.ajax.php文件也是controller文件，类中的方法做异步请求响应。
appname.sh.php文件也是controller文件，类中的方法做CLI响应。
appname.api.php文件可以看成是controller文件，类中的方法是对一个公共，或通用逻辑的封装，如获取图片完整路径。
```

### 4.3 关于类和方法
类命名：
> 除class文件中的直接以appname命名，其他的类文件都是appname+APPTYPE命名，APPTYPE需要大写。好做语法区分。

方法命名：
> 在方法声明中加前缀,说明方法的作用，如view类中的方法加html_getSimilarList(), model方法中加入db_getData()从数据库取，file_getData()从文件取,url_getData()从引擎或者远端取

### 4.4 关于变量和注释
类内变量, 参数入口变量 加_前缀, 跟过程中使用的局部变量，加以区分，增加可读性。

```
class similar{
public $_obj;
function do_getpage($_param){
}
}

文件注释 = 类注释
在文件开头加入，说明这个类的功能：
/**
* @package:
* @access: MixrenSystemBox.inc.php
* Summary: 系统 应用模板控制分配程序; 完成请求分析; 模块加载; 请求处理(执行);
* @Created: Fri Dec 25 16:41:02 CST 2009
* @Author: Zhurong
* @Generator: EditPlus2 & Dreamweaver & Zend & eclipse
*/

方法注释
/**
* 初始化请求
* param参数说明
**/
private function _parseApp(){
$this->_queryStr = urldecode($_SERVER["REQUEST_URI"]);
$this->_paramArr = explode( '/', trim(strtok($this->_queryStr,'?'),'/'));
//分配请求模块
$appName = DEFAULT_APP_NAME;
$this->_className = $appName;
$this->_appFile = APP_PATH . "{$appName}/{$appName}.controller.php";
$this->_method = empty($this->_paramArr[0]) ?  DEFAULT_APP_METHOD : $this->_paramArr[0];
$this->_method = "do_{$this->_method}";
}

类结尾注释
class Bin{
}//end class
```

为主要过程，算法，加入注释，方便维护和阅读。

在上线前会通过上线脚本对文件进行 php -w 去注释过程，所以注释写的长不占用程序执行过程中的加载内存。

# 5.代码监测

专门的代码扫描脚本。

扫描/website/app/下的目录，文件，方法，注释量。

出报告，多少应用目录； 多少同步响应类；多少异步响应类；多少同步页面；多少异步接口；多少逻辑接口；多少model接口；多少模板；各个文件的代码注释率；等等。

基于以上设计思想建立了从文件夹-》分段类文件-》功能模块方法 的树形结构设计程序架构,最大化实现CAP原则 consistency(一致性)，availability(可复用),Partition(有效划分)

让PHP代码更好的积累。

模板(纯html文件，呈现配置),程序数据配置文件是被加载到程序中，而不是现在将程序加载到html中一段段解释执行。

