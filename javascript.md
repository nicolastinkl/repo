# JavaScript 风格指南/编码规范



   * 基本类型：当你访问基本类型时，你是直接对它的值进行操作。



   * string
   * number
   * boolean
   * null
   * undefined



var foo = 1,
bar = foo;
bar = 9;
console.log(foo, bar); // => 1, 9


   * object
   * array
   * function





var foo = [1, 2],
bar = foo;
bar[0] = 9;
console.log(foo[0], bar[0]); // => 9, 9

   * 使用字面量语法来创建对象
   *



// bad
var item = new Object();
// good
var item = {};


   * 不要使用保留字作为“键值”，因为在IE8不能运行。More info
   *



// bad
var superman = {
default: { clark: 'kent' },
private: true
};
// good
var superman = {
defaults: { clark: 'kent' },
hidden: true
};
   * 使用容易理解的同义词来替代保留字
   *



// bad
var superman = {
class: 'alien'
};
// bad
var superman = {
klass: 'alien'
};
// good
var superman = {
type: 'alien'
};
   * 使用字面量语法来创建数组
   *



// bad
var items = new Array();
// good
var items = [];
   * 如果你不知道数组长度，数组添加成员使用push方法。
   *



var someStack = [];
// bad
someStack[someStack.length] = 'abracadabra';
// good
someStack.push('abracadabra');
   * 当你需要复制一个数组时，使用数组的slice方法。jsPerf
   *



var len = items.length,
itemsCopy = [],
i;
// bad
for (i = 0; i < len; i++) {
itemsCopy[i] = items[i];
}
// good
itemsCopy = items.slice();
   * 当你需要把“类似数组对象”转为数组时，使用数组的slice方法。
   *



function trigger() {
var args = Array.prototype.slice.call(arguments);
...
}
   * 字符串使用单引号‘’
   *



// bad
var name = "Bob Parr";
// good
var name = 'Bob Parr';
// bad
var fullName = "Bob " + this.lastName;
// good
var fullName = 'Bob ' + this.lastName;
   * 大于80个元素的字符串需要通过分隔符进行多行操作。
   * 注意：如果在长字符串中过度使用分隔符会影响性能。. jsPerf & Discussion
   *



// bad
var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
// bad
var errorMessage = 'This is a super long error that was thrown because
of Batman. When you stop to think about how Batman had anything to do
with this, you would get nowhere
fast.';
// good
var errorMessage = 'This is a super long error that was thrown because ' +
'of Batman. When you stop to think about how Batman had anything to do ' +
'with this, you would get nowhere fast.';
   * 通过编程的方式创建字符串，应该使用数组的join方法，而不是字符串链接方法。特别是对于IE而言。 jsPerf.
   *



var items,
messages,
length,
i;
messages = [{
state: 'success',
message: 'This one worked.'
}, {
state: 'success',
message: 'This one worked as well.'
}, {
state: 'error',
message: 'This one did not work.'
}];
length = messages.length;
// bad
function inbox(messages) {
items = '<ul>';
for (i = 0; i < length; i++) {
items += '<li>' + messages[i].message + '</li>';
}
return items + '</ul>';
}
// good
function inbox(messages) {
items = [];
for (i = 0; i < length; i++) {
items[i] = messages[i].message;
}
return '<ul><li>' + items.join('</li><li>') + '</li></ul>';
   * 函数表达式
   *



// anonymous function expression
var anonymous = function() {
return true;
};
// named function expression
var named = function named() {
return true;
};
// immediately-invoked function expression (IIFE)
(function() {
console.log('Welcome to the Internet. Please follow me.');
})();
   * 不要直接在非函数块（if，while等）里声明一个函数，最好将函数赋值给一个变量。虽然浏览器允许你在非函数块中声明函数，但是由于不同的浏览器对Javascript的解析方式不同，这样做就很可能造成麻烦。
   * 注意：ECMA-262 将块定义为一组语句，而函数声明不是语句。Read ECMA-262′s note on this issue
   *



// bad
if (currentUser) {
function test() {
console.log('Nope.');
}
}
// good
var test;
if (currentUser) {
test = function test() {
console.log('Yup.');
};
}
   * 不要将参数命名为arguments，它将在每个函数的作用范围内优先于arguments对象。
   *



// bad
function nope(name, options, arguments) {
// ...stuff...
}
// good
function yup(name, options, args) {
// ...stuff...
}
   * 使用点符号 . 来访问属性
   *



var luke = {
jedi: true,
age: 28
};
// bad
var isJedi = luke['jedi'];
// good
var isJedi = luke.jedi;
   * 当你在变量中访问属性时，使用[ ]符号
   *



var luke = {
jedi: true,
age: 28
};
function getProp(prop) {
return luke[prop];
}
var isJedi = getProp('jedi');
   * 使用var来声明变量，否则将声明全局变量，我们需要尽量避免污染全局命名空间，Captain Planet这样警告过我们。
   *



// bad
superPower = new SuperPower();
// good
var superPower = new SuperPower();
   * 多个变量时只使用一个var声明，每个变量占一新行。
   *



// bad
var items = getItems();
var goSportsTeam = true;
var dragonball = 'z';
// good
var items = getItems(),
goSportsTeam = true,
dragonball = 'z';
   * 最后再声明未赋值的变量，这对你之后需要依赖之前变量的变量赋值会有帮助。
   *



// bad
var i, len, dragonball,
items = getItems(),
goSportsTeam = true;
// bad
var i, items = getItems(),
dragonball,
goSportsTeam = true,
len;
// good
var items = getItems(),
goSportsTeam = true,
dragonball,
length,
i;
·         在范围内将变量赋值置顶，这有助于避免变量声明和赋值提升相关的问题
// bad
function() {
test();
console.log('doing stuff..');
//..other stuff..
var name = getName();
if (name === 'test') {
return false;
}
return name;
}
// good
function() {
var name = getName();
test();
console.log('doing stuff..');
//..other stuff..
if (name === 'test') {
return false;
}
return name;
}
// bad
function() {
var name = getName();
if (!arguments.length) {
return false;
}
return true;
}
// good
function() {
if (!arguments.length) {
return false;
}
var name = getName();
return true;
}
   * 变量声明在范围内提升，但赋值并没有提升
   *



// we know this wouldn't work (assuming there
// is no notDefined global variable)
function example() {
console.log(notDefined); // => throws a ReferenceError
}
// creating a variable declaration after you
// reference the variable will work due to
// variable hoisting. Note: the assignment
// value of `true` is not hoisted.
function example() {
console.log(declaredButNotAssigned); // => undefined
var declaredButNotAssigned = true;
}
// The interpreter is hoisting the variable
// declaration to the top of the scope.
// Which means our example could be rewritten as:
function example() {
var declaredButNotAssigned;
console.log(declaredButNotAssigned); // => undefined
declaredButNotAssigned = true;
}
   * 匿名函数表达式提升变量名，但函数赋值并没有提升，
   *



· function example() {
console.log(anonymous); // => undefined
anonymous(); // => TypeError anonymous is not a function
var anonymous = function() {
console.log('anonymous function expression');
};
}
   * 命名函数表达式提升变量名，但函数名字和函数体并没有提升。
   *



function example() {
console.log(named); // => undefined
named(); // => TypeError named is not a function
superPower(); // => ReferenceError superPower is not defined
var named = function superPower() {
console.log('Flying');
};
}
// the same is true when the function name
// is the same as the variable name.
function example() {
console.log(named); // => undefined
named(); // => TypeError named is not a function
var named = function named() {
console.log('named');
}
}
   * 函数声明能提升他们的函数名和函数体
   *



function example() {
superPower(); // => Flying
function superPower() {
console.log('Flying');
}
}
   * 更多的信息在JavaScript Scoping & Hoisting by Ben Cherry


   * 条件表达式和相等
   * 条件表达式强制使用 ToBoolean方法进行解析，并遵从以下简单的规则Object ：返回 true
   * Undefined： 返回 false
   * Null： 返回 false
   * Booleans ：返回 boolean的值
   * Numbers ：如果是+0, -0, or NaN返回 false, 其他则 true
   * Strings ：空字符串''返回 false 其他返回true
   *



if ([0]) {
// true
// An array is an object, objects evaluate to true
}
   * 使用简易方式
   *



// bad
if (name !== '') {
// ...stuff...
}
// good
if (name) {
// ...stuff...
}
// bad
if (collection.length > 0) {
// ...stuff...
}
// good
if (collection.length) {
// ...stuff...
}
   * 更多的信息查看 Truth Equality and JavaScript by Angus Croll


   * 在多行块中使用大括号





// bad
if (test)
return false;
// good
if (test) return false;
// good
if (test) {
return false;
}
// bad
function() { return false; }
// good
function() {
return false;
}

   * 多行注释使用 /** ... */ ，包括描述，指定类型、所有参数的值和返回值
   *



// bad
// make() returns a new element
// based on the passed in tag name
//
// @param <String> tag
// @return <Element> element
function make(tag) {
// ...stuff...
return element;
}
// good
/**
* make() returns a new element
* based on the passed in tag name
*
* @param <String> tag
* @return <Element> element
*/
function make(tag) {
// ...stuff...
return element;
}


   * 单行注释使用 //, 在注释的内容前另起一行进行单行注释，并在注释前留一空行。
   *



// bad
var active = true;  // is current tab
// good
// is current tab
var active = true;
// bad
function getType() {
console.log('fetching type...');
// set the default type to 'no type'
var type = this._type || 'no type';
return type;
}
// good
function getType() {
console.log('fetching type...');
// set the default type to 'no type'
var type = this._type || 'no type';
return type;
}
   * 在你的注释加上FIXME或TODO的前缀可以帮助其他开发者迅速理解你指出的问题和需要的帮助，以及你建议需要完善的解决问题，这跟常规注释不一样因为其具有可行动性，FIXME——是需要解决的而TODO——是需要完善的
   * 使用// FIXME: 来标记问题
   *



function Calculator() {
// FIXME: shouldn't use a global here
total = 0;
return this;
}
   * 使用 // TODO：给待解决问题进行标注
   *



function Calculator() {
// TODO: total should be configurable by an options param
this.total = 0;
return this;
}
   * 使用tabs设置两个空格
   *



// bad
function() {
∙∙∙∙var name;
}
// bad
function() {
∙var name;
}
// good
function() {
∙∙var name;
}
   * 分支前面空一格
   *



// bad
function test(){
console.log('test');
}
// good
function test() {
console.log('test');
}
// bad
dog.set('attr',{
age: '1 year',
breed: 'Bernese Mountain Dog'
});
// good
dog.set('attr', {
age: '1 year',
breed: 'Bernese Mountain Dog'
});
   * 操作符前后空一格
   *



// bad
var x=y+5;
// good
var x = y + 5;
   * 文件末尾用换行符结束
   *



// bad
(function(global) {
// ...stuff...
})(this);
// bad
(function(global) {
// ...stuff...
})(this);
// good
(function(global) {
// ...stuff...
})(this)
   * 使用长方法链时使用缩进
   *



// bad
$('#items').find('.selected').highlight().end().find('.open').updateCount();
// good
$('#items')
.find('.selected')
.highlight()
.end()
.find('.open')
.updateCount();
// bad
var leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
.attr('width',  (radius + margin) * 2).append('svg:g')
.attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
.call(tron.led);
// good
var leds = stage.selectAll('.led')
.data(data)
.enter().append('svg:svg')
.class('led', true)
.attr('width',  (radius + margin) * 2)
.append('svg:g')
.attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
.call(tron.led);
   * 不要在句首使用逗号
   *



// bad
var once
, upon
, aTime;
// good
var once,
upon,
aTime;
// bad
var hero = {
firstName: 'Bob'
, lastName: 'Parr'
, heroName: 'Mr. Incredible'
, superPower: 'strength'
};
// good
var hero = {
firstName: 'Bob',
lastName: 'Parr',
heroName: 'Mr. Incredible',
superPower: 'strength'
};
   * 不要使用多余的逗号，这在IE6/7 和 IE9的怪异模式中会造成问题，同样，在ES3中有些实现，如果使用多余的逗号将增加数组的长度，这在ES5中有阐明(source):
   *



// bad
var hero = {
firstName: 'Kevin',
lastName: 'Flynn',
};
var heroes = [
'Batman',
'Superman',
];
// good
var hero = {
firstName: 'Kevin',
lastName: 'Flynn'
};
var heroes = [
'Batman',
'Superman'
];
   *



// bad
(function() {
var name = 'Skywalker'
return name
})()
// good
(function() {
var name = 'Skywalker';
return name;
})();
// good (guards against the function becoming an argument when two files with IIFEs are concatenated)
;(function() {
var name = 'Skywalker';
return name;
})();
   * 转型&强制
   *
      * 在语句的开头执行强制转型。
   * Strings:
   *



//  => this.reviewScore = 9;
// bad
var totalScore = this.reviewScore + '';
// good
var totalScore = '' + this.reviewScore;
// bad
var totalScore = '' + this.reviewScore + ' total score';
// good
var totalScore = this.reviewScore + ' total score';
   * 使用parseInt对Numbers型进行强制转型。
   *



var inputValue = '4';
// bad
var val = new Number(inputValue);
// bad
var val = +inputValue;
// bad
var val = inputValue >> 0;
// bad
var val = parseInt(inputValue);
// good
var val = Number(inputValue);
// good
var val = parseInt(inputValue, 10);
   * 如果出于某种原因你需要做些疯狂的事，而parseInt是你的瓶颈，出于性能考虑你需要使用位移，那么留下你的注释来解释原因。
   *



// good
/**
* parseInt was the reason my code was slow.
* Bitshifting the String to coerce it to a
* Number made it a lot faster.
*/
var val = inputValue >> 0;
   * 注意：小心位移操作符，Numbers代表着64位的值，而位移操作符只能返回32位的整型，位移对于大于32位的整型的值会有不好的影响，32位最大的有符号整型是2,147,483,647。
   * （有关讨论：Discussion）
   *



2147483647 >> 0 //=> 2147483647
2147483648 >> 0 //=> -2147483648
2147483649 >> 0 //=> -2147483647
   * Booleans:
   *



var age = 0;
// bad
var hasAge = new Boolean(age);
// good
var hasAge = Boolean(age);
// good
var hasAge = !!age;
   * 不要使用一个字母命名，而应该用单词描述
   *



// bad
function q() {
// ...stuff...
}
// good
function query() {
// ..stuff..
}
   * 使用驼峰法来给对象、函数、实例命名。
   *



// bad
var OBJEcttsssss = {};
var this_is_my_object = {};
function c() {}
var u = new user({
name: 'Bob Parr'
});
// good
var thisIsMyObject = {};
function thisIsMyFunction() {}
var user = new User({
name: 'Bob Parr'
});
   * 使用驼峰式大小写给构造函数和类命名。
   *



// bad
function user(options) {
this.name = options.name;
}
var bad = new user({
name: 'nope'
});
// good
function User(options) {
this.name = options.name;
}
var good = new User({
name: 'yup'
});
   * 使用下划线前缀_来命名私有属性。
   *



// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
// good
this._firstName = 'Panda';
   * 储存this的引用使用_this
   *



// bad
function() {
var self = this;
return function() {
console.log(self);
};
}
// bad
function() {
var that = this;
return function() {
console.log(that);
};
}
// good
function() {
var _this = this;
return function() {
console.log(_this);
};
}
   * 给你的函数命名，这有助于堆栈重写
   *



// bad
var log = function(msg) {
console.log(msg);
};
// good
var log = function log(msg) {
console.log(msg);
};
   * 注意：IE8以下还有一些关于命名函数表达式的怪癖。详情见http://kangax.github.io/nfe/


   * 如果你创建访问函数使用getVal() 和 setVal(‘hello’)
   *



// bad
dragon.age();
// good
dragon.getAge();
// bad
dragon.age(25);
// good
dragon.setAge(25);

如果这个属性是boolean，使用isVal() 或者 hasVal()
// bad if (!dragon.age()) { return false; } // good if (!dragon.hasAge()) { return false; }
.也可以使用get() 和 set()函数来创建，但是必须一致。

   *
      *



function Jedi(options) {
options || (options = {});
var lightsaber = options.lightsaber || 'blue';
this.set('lightsaber', lightsaber);
}
Jedi.prototype.set = function(key, val) {
this[key] = val;
};
Jedi.prototype.get = function(key) {
return this[key];
};
      * 给原型对象添加方法，相比用新对象重写原型，重写原型会有继承问题。如果你要重写原型方法，请重写基础方法。
      *



function Jedi() {
console.log('new jedi');
}
// bad
Jedi.prototype = {
fight: function fight() {
console.log('fighting');
},
block: function block() {
console.log('blocking');
}
};
// good
Jedi.prototype.fight = function fight() {
console.log('fighting');
};
Jedi.prototype.block = function block() {
console.log('blocking');
};
      * 方法返回this有助于方法链
      *



// bad
Jedi.prototype.jump = function() {
this.jumping = true;
return true;
};
Jedi.prototype.setHeight = function(height) {
this.height = height;
};
var luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined
// good
Jedi.prototype.jump = function() {
this.jumping = true;
return this;
};
Jedi.prototype.setHeight = function(height) {
this.height = height;
return this;
};
var luke = new Jedi();
luke.jump()
.setHeight(20);
      * 可以重写常规的toString()方法。但必须保证可以成功运行并没有别的影响
      *



function Jedi(options) {
options || (options = {});
this.name = options.name || 'no name';
}
Jedi.prototype.getName = function getName() {
return this.name;
};
Jedi.prototype.toString = function toString() {
return 'Jedi - ' + this.getName();
};
      * 将为事件加载数据时（不管是DOM事件还是其他专用事件的，比如Backbone事件）用散列表来取代原始值。因为这将允许后续添加更多的数据载入事件而不用更新每个事件的处理程序。例如：





// bad
$(this).trigger('listingUpdated', listing.id);
...
$(this).on('listingUpdated', function(e, listingId) {
// do something with listingId
});
更好的方式：




// good
$(this).trigger('listingUpdated', { listingId : listing.id });
...
$(this).on('listingUpdated', function(e, data) {
// do something with data.listingId
});

   * 模块应该以a！开始，以确保当模块忘记包含最后一个分号时，在脚本连接时不会报错。
   * 文档需要用驼峰法命名，同一文件内要用一样的名字以及要与单个输出相匹配。
   * 增加一个叫noConflict()的方法，使模块输出早期版本并返回。
   * 在模块开始的部位声明'use strict'。

   *



// fancyInput/fancyInput.js
!function(global) {
'use strict';
var previousFancyInput = global.FancyInput;
function FancyInput(options) {
this.options = options || {};
}
FancyInput.noConflict = function noConflict() {
global.FancyInput = previousFancyInput;
return FancyInput;
};
global.FancyInput = FancyInput;
}(this);

jQuery

   * JQuery对象变量前缀使用$
   *



// bad
var sidebar = $('.sidebar');
// good
var $sidebar = $('.sidebar');
   * jQuery缓存查找
   *



// bad
function setSidebar() {
$('.sidebar').hide();
// ...stuff...
$('.sidebar').css({
'background-color': 'pink'
});
}
// good
function setSidebar() {
var $sidebar = $('.sidebar');
$sidebar.hide();
// ...stuff...
$sidebar.css({
'background-color': 'pink'
});
}
   * 在DOM查询中使用层叠样式 $('.sidebar ul')或parent > child $('.sidebar > ul'). jsPerf


   * 使用find来查找jQuery对象
   *



// bad
$('ul', '.sidebar').hide();
// bad
$('.sidebar').find('ul').hide();
// good
$('.sidebar ul').hide();
// good
$('.sidebar > ul').hide();
// good
$sidebar.find('ul').hide();
