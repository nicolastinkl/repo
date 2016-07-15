# js无阻塞加载
>##核心:
-  加载js脚本时,被加载的js脚本不会阻塞UI线程的执行和以阻塞方式加载的脚本.
-  动态的创建script的dom节点

- ###XHR Eval:通过XHR读取脚本，通过Eval令脚本生效。

```
var xhrObj = new XMLHttpRequest();
xhrObj.onreadystatechange = function(){
    if(xhrObj.readyState == 4 && 200 == xhrObj.status){
        eval(xhrObj.responseText);
    }
};

xhrObj.open("GET", "A.js", true);
xhrObj.send("");
```
`由于XMLHttpRequest本身不能跨域，所以该方法不能跨域。`

- ###XHR Injection:使用动态创建script元素，来写入脚本，在某些情况下可能比上一种方法要快些

```
var xhrObj = new XMLHttpRequest();
xhrObj.onreadystatechange = function(){
    if(xhrObj.readyState == 4){
        var scriptElem = document.createElement("script");
        document.getElementsByTagName("head")[0].appendChild(scriptElem);
        scriptElem.text = xhrObj.responseText;
    }
};
xhrObj.open("GET", "A.js", true);
xhrObj.send("");
```

-  ###Script in Iframe:由于Iframe是开销最高的DOM元素，这种方法还是尽量避免使用，而且这种方法也无法实现跨域

- ###Script DOM Element:可跨域方案，利用动态插入script元素来让脚本读取、生效。

```
var scriptElem = document.createElement("script");
scriptElem.src = "http://anydomain.com/A.js";
document.getElementByTagName("head")[0].appendChild(scriptElem);
```

- ###Script Defer:原生方案。利用defer属性来防止脚本阻塞。
`<script defer src="A.js"></script>`
>许多浏览器不支持该属性。

- ###document.write Script Tag:动态写脚本的另一种方案，不过只在IE中是并行下载的。

```document.write("<script type='text/javascript' src='A.js'></script>");```

##最好的方案就是xhr 注入和script dom element

> 无阻塞脚本的好处就是不会阻塞UI的执行，也不会影响其他同步js代码的执行，不过无阻塞脚本改变了脚本的加载顺序，所以在使用无阻塞脚本时候一定要更加注意脚本之间的依赖关系，保证整个页面的脚本都能正常执行。




#处理依赖脚本"变量未定义"的问题
>例如jQuery没有提前加载，因此使用$时候提示$变量未定义.

###思路
>让那些依赖于无阻塞加载的脚本的js代码在脚本加载完毕后才会执行，我们需要一个办法将无序的脚本加载变得有序

推荐的两种方法都是使用dom技术创建script节点，然后将该节点加入到文档的head头部，对于script节点，在非ie浏览器下有一个onload事件，该事件会在script加载完毕后才会执行，ie浏览器下有onreadystatechange事件，而ie下script的dom节点有一个readystate属性，它的取值如下:
1. uninitialized（未初始化）：对象存在尚未初始化；
2. loading（正在加载）：对象正在加载数据；
3. loaded（加载完毕）：对象数据加载完成
4. interactive（交互）：可以操作对象，但是还没有完全加载
5. complete（完成）：对象已经加载完毕。

```
scriptNode.onreadystatechange = function(){
    if (scriptNode.readystate == 'complete'){// todo......}
}
```


资源
- [requirejs.org](requirejs.org)
    -  [http://www.ruanyifeng.com/blog/2012/11/require_js.html](http://www.ruanyifeng.com/blog/2012/11/require_js.html)
    - [github](https://github.com/jrburke/requirejs)
