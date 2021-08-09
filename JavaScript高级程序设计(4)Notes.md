

# 1.JavaScript介绍

### JavaScript实现

```
完整的JavaScript实现包含以下几个部分：  

核心：（ECMAScript）

扩展： DOM（文档对象模型） ：提供与网页内容交互的方法和接口。

      BOM （浏览器对象模型）：提供与浏览器交互的方法和接口。
```

# 2.HTML中的JavaScript

### <script'>标签元素 

#### 可选属性：

 **async:** 不用等待脚本下载执行完后再加载页面，也不需要等待异步脚本加载完成后再加载其他脚本。

​      有async标签的脚本不能保证按照出现的次序执行。

**charset**: src指定的代码的字符集

**crossorigin**: 配置相关请求的跨资源共享设置(CORS)。默认不使用。crossorigin="anonymous"配置文件请求不必设置凭据标志。crossorigin="use-credentials"设置凭据标志，出站请求会包含凭据

**defer**: 表示脚本可以等到文档解析或显示完成后再执行。 浏览器会忽略行内脚本的defer属性

**intergrity**: 允许比对接受到的资源和指定的加密签名以验证资源的完整性。签名不匹配会报错。

**type**: 代替language属性，始终是"text/javascript"

​     如果值是"module"，代码会被当成ES6模块且只有这种情况代码中才能出现import/export关键字

**src**:  外部文件地址。使用src属性的<script>元素，会忽略标签内的行内代码。

​     浏览器不会检查外部文件的扩展名，但是要保证返回正确的MINE类型。

#### **行内脚本如果包含</script>字符串时，需要添加转义字符。如：**

```html
<script>
  function test () {
     //console.log("</script>");
     console.log("<\/script>");
  }
</script>
```

### <script'>标签位置

<script>元素至于页面的<head>标签内，目的主要是把外部的css和js文件集中到一起。但是这样有一个不好的地方。    必须把所有的JavaScript代码下载、解析和解释完成以后，才能开始渲染页面。对于大量外部js引入的页面，加载的时间内浏览器窗口将完全空白。
优化方式一般是，把<script>元素移植<body>标签内的结尾处。
当然也可以给<head>标签中的 ！！外部！！ <script>元素添加defer属性。
或者添加async属性来异步加载。

### <script'>元素的加载顺序

**1.HTML5规范要求脚本按照出现的顺序执行。**

**2.如果外部脚本申明了defer属性，它们会在浏览器执行到</html>标签结束后才会执行，且如果有多个defer属性，按照规范也会按照出现顺序相应地延迟执行。**

**如果外部脚本申明了defer属性，它们会在浏览器执行到</html>标签结束后且在页面内的其他无defer属性的<script>执行后才会执行，且如果有多个defer属性，按照规范也会按照出现顺序相应地延迟执行。**

**3.HTML5 规范要求脚本按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于 DOMContentLoaded 事件执行。在现实当中，延迟脚本并不一定会按照顺序执行，也不一定会在 DOMContentLoaded 事件触发前执行，因此最好只包含一个延迟脚本。(defer)**

​		个人测试没有出现过延后的情况。个人感觉书上这么写的原因可能是因为， HTML5规范如此，但是每个浏览器厂商未必会按照这个规范实现(有待考究)

**4.async 只适用于外部脚本文件，并告诉浏览器立即下载文件，下载完成后立即执行。但与 defer不同的是，标记为 async 的脚本并不保证按照指定它们的先后顺序执行。async脚本可以保证在load事件前执行，但是有可能在DOMContentLoaded事件之前或之后。**

​		有关DOMContentLoaded 与 load区别，可以参见[演示链接](https://testdrive-archive.azurewebsites.net/HTML5/DOMContentLoaded/Default.html)。

**5.（补充）async和defer的区别**

​		这里引用一下知乎@ 编译青春的文章，讲的比较详细与直观 ： [你不知道的 DOMContentLoaded](https://zhuanlan.zhihu.com/p/25876048)

### 动态加载脚本

![img](https://upload-images.jianshu.io/upload_images/26063418-4020145be24f63d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

​		以这种方式获取的资源对浏览器的预加载器是不可见的，这回严重影响到它们在资源获取队列中的优先级。

​		想让预加载器知道这些动态请求文件的存在，可以再文档头部显示申明它们：

```html
<link rel="preload" href="gibberish.js">
```

### XHTML中的变化

XHTML中，行内脚本小于号(<)会被解释成标签开始，所以需要替换成（&lt;）

或者使用 

```xhtml
<![CDATA[ "   " ]]> 
```

来包裹行内脚本。

------

### <noscript'>元素

不支持JavaScript时会显示标签内元素。

# 3.语言基础

## 3.4数据类型

###  简单数据类型（原始类型）

#### Undefined

​	Undefined类型只有undefined一个值

```js
	null == undefined //true
```

​	undefined的目的就是明确空对象指针和未初始化变量的区别

typeof undefined的变量和未声明的变量都是undefined

**不确定null或者undefined可以使用String()来转换**

#### Null

​	Null类型只有一个null值，逻辑上是一个空对象的指针

#### Boolean

```
Boolean(变量) 通过构造函数强制转换变量
Boolean       true                      false
String        非空字符串			         ""
Number		  非零数值包括Infinity   	 	0、NaN
Object 		  任意对象                   null
Undefined     只会变成false
```

#### Number

最大值最小值在Number.MIN_VALUE和Number.MAX_VALUE中

0.1+0.2 ！= 0.3是因为使用了IEEE754数值造成的精度问题

默认情况下，ECMAScript会讲小数点后至少包含6个零的浮点值转换为科学计数法

isFinite(变量)判断是否超出范围

Number.POSITIVE_INFINITY Number.NEGATIVE_INFINITY 值为正负Infinity

isNaN(变量)任意值判断是不是“不是数值”

```js
console.log(isNaN(NaN)); //true
console.log(isNaN(10)); //false
console.log(isNaN("10")); //false 可以转换为10
console.log(isNaN("blue")); //true 不可以转换为数值
console.log(isNaN(true)); //false 可以转换为数值1
```

```
isNaN()可以用来测试对象，首先调用对象的valueOf()方法，然后确定返回值能否转换为数值，如果不能，再调用toString()方法测试返回值
这是通常的ECMAScript内置函数和操作符的工作方式
```

##### 数值转换

###### Number()

```js
// 布尔值转换为01
// 数值直接返回
// null返回0
// undefined返回NaN
// 字符串:
包含数值字符包括加减号，转换为十进制数值
包含有效浮点数格式，转换为浮点数
"0x,"转换为16进制对应的10进制的数值
空字符串转换为0
其他NaN
// 对象
同isNaN，先调用对象的valueOf()，如果转换结果是NaN，则调用toString然后按照字符串格式转换
```

###### parseInt()

```js
// 从第一个不是空格字符开始转换，第一个字符不是数值字符加号或者减号，则立即返回NaN （Number空字符串转换是0）；一直转换到非字符为止
// 0x开头默认16进制，0开头默认8进制
//parseInt可以传第二个参数表示进制
parseInt('0xaf') === parseInt('AF',16)  //175
parseInt('AF') //NaN
```

###### parseFloat()

```js
// 类似parseInt 识别所有浮点格式以及十进制格式(忽略开头的0) 
// 只解析10进制 16进制始终返回0
// 可以解析科学计数法字符串
```

#### String

\n换行，\t制表，\b退格，\r回车,  \f换页,  ...

ES中，字符串是不可变的，一旦创建如果修改，则需要销毁原始字符串，然后包含新值的另一字符串保存到变量

几乎所有值都有toString()方法

toString(n)表示转换为进制的字符串

##### 格式转换

###### String()

```
// 如果值有toString方法则调用该方法不传参数
// null返回"null" undefined返回"undefined"
```

###### 加号操作符加上空字符串

##### 模版字面量

所有模版字面量中的值都会被toString强转成字符串，可以调用函数方法

模版字面量标签函数

```js
function simpleTag(strings,...expressions){
	console.log(strings)
	for(const expression of exporessions){
		console.log(expression)
	}
	return 'foobar'
}
let a=6,b=9
let tag = simpleTag`${ a } + ${ b } = ${ a + b }`
//["", " + ", " = ", ""]
//6
//9
//15
```

模版字面量里原始字符串会被识别（换行符，Unicode等等)

使用String.raw标签函数来处理，但是原始换行符不会被阻断

```js
console.log(`firstline\nsecondline`)
//firstline
//secondline
console.log(String.raw`firstlin\nsecondline`)
//firstline\nsecondline
console.log(`firstline
secondline`)
//firstline
//secondline
console.log(String.raw`firstline
secondline`)
//firstline
//secondline
```

也可以使用标签函数的第一个参数，字符串数组的.raw属性来取得每个字符串的原始内容

```js
function printRaw(strings){
    console.log('actual')
    for(const string of strings){
        cosnoel.log(string)
    }
    console.log('escaped')
    for(const rawString of strings.raw){
        console.log(rawString)
    }
}
printRaw`\u00A9${ 'and'}\n`
//actual
//©
//escaped
//\u00A9
//\n
```

#### Symbol

符号实例是唯一不可变的

用途是确保对象属性使用唯一标识符，不会发生属性冲突危险

符号类似私有属性，**但是并不是为了提供私有属性行为才增加的**，相反，符号就是用来创建唯一记号，进而用作非字符串形式的对象属性

##### ①符号基本用法

使用Symbol()函数初始化

Symbol('description') decription与符号的定义和标识无关，只用于调试

```
let symbo1 = Symbol('foo')
let symbo2 = Symbol('foo')
symbo1 == symbo2 //false
let symbo3 = Symbol()
console.log(symbo1) //Symbol(foo)
console.log(symbo2) //Symbol(foo)
console.log(symbo3) //Symbol()
```

**Symbol不能用作构造函数，与new关键字一起使用**为了避免创建符号包装对象

```
console.log(typeof new Boolean()) //object
//Number String同理
```

##### ②全局符号注册表

如果需要重用symbol实例，则以一个字符串为键，在全局符号注册表中创建并重用符号

使用Symbol.for()方法

```
let sym1 = Symbol.for('foo')
let sym2 = Symbol.for('foo')
let sym3 = Symbol('foo')
sym2 ===sym1 //true
sym2 === sym3 //false
```



# 17.事件

### 事件流：

**事件冒泡：**<div> -> <body> -> <html> -> <document> -> (window)

有些事件不支持冒泡：

**事件捕获:**   (window) -> <document> -> <html> -> <body> -> <div>

IE9, Firefox,Chrome,Safari的事件终点是window对象。

IE的事件流叫事件冒泡，事件捕获是Netscape Communicator团队提出的另一种事件流。

老版本浏览器不支持，所以事件捕获的使用场景较少。

**DOM2级事件开始标准化DOM事件**

规定，**DOM事件流**包括三阶段:事件捕获阶段，处于目标阶段，事件冒泡阶段。

![image-20210416105526442](https://images2015.cnblogs.com/blog/740839/201609/740839-20160910153551644-925968915.jpg)

在DOM事件流中，实际的目标(div)在捕获阶段不会接收到事件。在捕获阶段，事件从document到<html>再到<body>后就停止了。

然后是“处于目标”阶段,事件在<div>上发生，并在事件处理中被砍成冒泡阶段的一个部分。然后，冒泡阶段发生，事件又传播回文档。

多数实现DOM事件流的浏览器都实现了规范要求的“捕获阶段不涉及事件目标”

**但是在IE9,Safari,Chorme，Firefox和Opera9.5及更高版本都会在捕获阶段触发事件对象上的事件**

### 事件处理：

#### **①HTML事件处理：**

例如

```html
<input type="button" value='Click Me' onclick="alert('Clicked')" />
<input type="button" value='Click Me' onclick="showMessage(this,event)" />
//这么写会有“时差”问题（触发时showMessage并未解析），所以使用try-catch来捕获异常
<input type="button" value='Click Me' onclick="try{showMessage();}catch(ex){}" />
```

这样写有一个隐藏用法，就是扩展作用域的方式。

```html
<input type="button" value='Click Me' onclick="console.log(event)"/>
//click event
<input type="button" value='Click Me' onclick="console.log(this)"/>
//input本身
<input type="button" value='Click Me' onclick="console.log(value)"/> 
//'Click Me'
```

```html
<input type="button" id='username' value='dasdafdsa'/>
<input type="button" value='Click Me' onclick="console.log(username.value)"/>
//dasdafdsa
```

#### ②DOM0级事件处理程序

以这种方式添加的事件处理程序会在事件流的冒泡阶段被处理.

**0级对每个事件只支持一个事件处理程序**

```js
var btn = document.getElementById("myBtn");
btn.onclick = function() {
	alert(this.id);			//function的作用域在元素中 this是当前元素
}
btn.onclick = null; 		//删除事件处理程序
```

#### ③DOM2级事件处理程序

第三个布尔参数，表示事件在捕获阶段或者冒泡阶段调用处理程序(true捕获，false冒泡)

大多数情况下都是添加到事件流的冒泡阶段，这样可以最大限度地兼容各种浏览器

```js
var btn = document.getElementById("myBtn");
btn.addEventListener('click',function(){
	alert(this.id);
},false)
btn.addEventListener('click',function(){
	alert('hello world');
},false)
//会按照顺序触发，id->hello world
```

通过addEventListener()添加的事件处理程序只能使用removeEventListener()来移除

传入的参数相同，所以**！！匿名函数无法被移除(如上代码段中添加的事件)！！**

#### **④IE事件处理程序**

attachEvent和detachEvent

IE8及更早版本只支持冒泡，所以通过如上方式添加的事件处理程序都会被添加到冒泡阶段。

与0级和2级不同，它的**作用域是window**

```js
var btn = document.getElementById("myBtn");
btn.attachEvent('onclick',function(){
	alert('id'); //它的作用域是window
},false)
btn.attachEvent('onclick',function(){
	alert("hello world");
},false)
//与DOM2级相反 ，hello world->id
```

类似的，匿名函数无法被移除

### 兼容跨浏览器的事件处理写法：

```js
//定义
var EventUtil = {
  addHandler: function(element, type, handler){
  	  if(element.addEventListener){
          element.addEventListener(type, handler, false);
      }else if(element.attachEvent){
          element.attachEvent("on"+type, handler);
      }else{
          element["on"+type] = handler;
      }
  },
  removeHandler: function(element, type, handler){
      if(element.removeEventListener){
          element.removeEventListener(type, handler, false);
      }else if(element.detachEvent){
          element.detachEvent("on"+type, handler);
      }else{
          element["on"+type] = null;
      }
  }
};
//调用
var btn = document.getElementById("myBtn");
var handler = function(){
    alert("Clicked");
}
EventUtil.addHandler(btn, "click", handler);
EventUtil.removeHandler(btn, "click", handler);
```

### DOM事件对象：

event这个变量存储了event对象，**不管function参数里带没带event,都可以在事件处理函数中直接写event来获取作用域的event对象。**

在HTML事件处理中，onclick="func1()"是没有默认值的，func1(this)传this,则this指向对象本身。

而在DOM0级和2级处理中，添加的事件处理函数

handler=function(arg1){};中，function默认的第一个argument就是event对象。（**不可以填this**）

所以不管是function(a)还是function(e)，指向的都是event对象。

**事件处理程序执行期间，event对象才会存在；事件处理程序执行完成，event对象就会被销毁**

#### ①event通用对象属性

**bubbles:** 只读 boolean 事件是否冒泡;

**cancelable：**只读 boolean 事件是否可取消;

**composed:**只读 boolean 事件是否可以穿过[**ShadowDOM**](https://developer.mozilla.org/zh-CN/docs/conflicting/Web/Web_Components/Using_shadow_DOM)和常规DOM进行冒泡

**currentTarget:**只读 element 事件当前注册目标的引用(可能在重定向过程中被改变)

**target:**只读 element 事件的目标

```js
//target和currentTarget
//在事件处理程序内部，this始终=currentTarget,target只包含事件的实际目标
//如果一个i标签没有注册click事件，button注册了，那么点击i标签的时候，click事件冒泡到了button，这时候target和currentTarget是不同的
```

**defautPrevented:**只读 boolean 是否调用了preventDefault()方法取消了默认行为

**eventPhase:**只读 Integer 事件流正被处理到了哪个阶段(0没有事件正在被处理，1捕获阶段，2处于目标，3冒泡阶段)

**timeStamp:**只读 timestamp 事件创建时的时间戳（精度为毫秒）

**type:**只读 String 事件类型

**isTrusted:**只读 boolean 表示事件是由浏览器（例如用户点击）发起的(true)，还是由脚本（使用事件创建方法，例如 [`Event.initEvent`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/initEvent)）发出的。

**view：**只读 AbstractView  与事件关联的抽象视图，等同于发生事件的window对象 

#### **②浏览器差别实现导致的不同对象属性(举常见)**

**detail:**只读  Integer 事件细节，比如click是当前点击数，mousedown(up)是1+点击数，等等

（firefox和chrome对于滚轮事件的事件对象名不一样）

**path:**只读 Array 事件冒泡的路径对象 dom对象，顺序就是冒泡顺序（chrome实现了，firefox和safari,edge目前暂时没有）

```js
//获取event.path属性
/** 
* 需要获取事件冒泡的所有元素,Chrome有$event.path属性，FireFox和Safari没有
*/
function getEventPath($event) {
    //let path = ($event.composedPath && $event.composedPath()) || $event.path 2020/12/10不知道为什么composedPath()实装了但是返回值都是[]
    const path = $event.path,
        target = $event.target;
    if (path != null) {
        return (path.indexOf(window) < 0) ? path.concat(window) : path;
    }
    if (target === window) {
        return [window];
    }
    function getParents(node, memo) {
        memo = memo || [];
        const parentNode = node.parentNode;
        if (!parentNode) {
            return memo;
        } else {
            // console.log(memo);
            return getParents(parentNode, memo.concat(parentNode));
        }
    }
    return [target].concat(getParents(target), window);
}
```

#### ③对象方法

**preventDefault:**  cancelable=true可使用 取消事件的默认行为

**stopPropagation:** bubbles=true可使用 阻止事件的进一步捕获或者冒泡

**stopImmediatePropagation:**DOM3级事件新增 阻止事件进一步捕获或者冒泡，阻止任何事件处理程序被调用。

### IE事件对象

#### ①IE的event对象

访问IE中的event对象取决于指定事件处理程序的方法。

DOM0级添加（btn.onclick=function(){}),event对象作为window对象的一个属性存在

attachEvent()添加的时间作为参数传入事件处理程序函数中，也可以通过window对象属性访问。

通过HTML添加的和DOM中介绍的相同。

#### ②IE event对象属性

**cancelBubble:**  读写 boolean 默认false,设为true可以取消事件冒泡(stopPropagation())

**returnValue:** 读写 boolean 默认true，设为false可以取消事件默认行为(preventDefault())

**srcElement:**  只读 事件的目标(target)

**type:** String 被触发事件的类型

### 兼容跨浏览器的事件对象写法：

```js
var EventUtil = {
    addHandler: function(element, type, handler){
  	  if(element.addEventListener){
          element.addEventListener(type, handler, false);
      }else if(element.attachEvent){
          element.attachEvent("on"+type, handler);
      }else{
          element["on"+type] = handler;
      }
    },
    removeHandler: function(element, type, handler){
      if(element.removeEventListener){
          element.removeEventListener(type, handler, false);
      }else if(element.detachEvent){
          element.detachEvent("on"+type, handler);
      }else{
          element["on"+type] = null;
      }
    },
    getEvent: function(event){
        return event?event: window.event;
    },
    getTarget: function(event){
        return event.target||event.srcElement;
    },
    preventDefault: function(event){
        if(event.preventDefault){
            event.preventDefault(),
        }else{
            event.returnValue = false;
        }
    },
    stopPropagation: function(event){
        if(event.stopPropagation){
            event.stopPropagation();
        }else{
            event.cancelBubble = true;
        }
    },
    getRelatedTarget: function(event){
      	if(event.relatedTarget){
            return event.relatedTarget;
        }  else if(event.toElement){
            return event.toElement;
        }	else if(event.fromElement){
            return event.fromElement;
        }	else{
            return null;
        }
    },
};
//调用
var btn = document.getElementById("myBtn");
btn.onclick = function(event){
	event = EventUtil.getEvent(event);
    var target = EventUtil.getTarget(event);
    EventUtil.preventDefault(event);
    EventUtil.stopPropagation(event);
};
```

### 事件类型：

DOM 3级事件在DOM 2级事件的基础上添加了更多的事件类型,全部类型如下:

```text
UI事件，当用户与页面上的元素交互时触发，如：load、scroll、resize、select、abort、unload
焦点事件，当元素获得或失去焦点时触发，如：blur、focus
鼠标事件，当用户通过鼠标在页面执行操作时触发如：dbclick、mouseup
滚轮事件，当使用鼠标滚轮或类似设备时触发，如：mousewheel
文本事件，当在文档中输入文本时触发，如：textInput
键盘事件，当用户通过键盘在页面上执行操作时触发，如：keydown、keypress
合成事件，当为IME（输入法编辑器）输入字符时触发，如：compositionstart
变动事件，当底层DOM结构发生变化时触发，如：DOMsubtreeModified
同时DOM3级事件也允许使用者自定义一些事件。
```

```js
var isSupported = document.implementation.hasFeature("MouseEvents","2.0");
var isSupported = document.implementation.hasFeature("MouseEvent","3.0");
```



![preview](https://segmentfault.com/img/bVbrRT3?w=999&h=636/view)



#### **keydown,keypress,keyup**事件

keydown,任意键，keyup,松开任意键,keypress,字符键

顺序 keydown->keypress;松开->keyup

**event.keycode,Firefox和Opera中分号值为59，IE和Safari为186**

IE9,Firefox,Chrome,Safari支持charCode属性，为字符的ASCII码。如果想跨平台使用则需要检测charCode属性是否可用。**（DOM3级事件移除）**

String.fromCharCode()可以转换为实际的字符。

#### textInput事件

只有可编辑区域才能触发此事件，有data属性，值是输入的字符而非字符编码(有大小写和shift)

#### 变化事件

```
var isSupported = document.implementation.hasFeature("MutationEvents","2.0")
```

DOM2级定义了如下变动事件：

- **DOMSubtreeModifined**:在DOM结构发生任何变化的时候。这个事件在其他事件触发后都会触发。
- **DOMNodeInserted**:当一个节点作为子节点被插入到另一个节点中时触发。
- **DOMNodeRemoved**:在节点从其父节点中移除时触发。
- **DOMNodeInsertedIntoDocument**：在一个节点被直接插入文档或通过子树间接插入文档之后触发。这个事件在DOMNodeInserted之后触发。
- **DOMNodeRemovedFromDocument**:在一个节点被直接从文档移除或通过子树间接从文档移除之**前**触发。这个事件在DOMNodeRemoved之后触发。
- **DOMAttrModified**：在特性被修改之后触发。
- **DOMCharacterDataModified**:在文本节点的值发生变化时触发。

举例：删除一个节点的事件触发顺序

DOMNodeRemoved(冒泡) -> DOMNodeRemovedFromDocument(被移除节点和子节点触发，不冒泡) -> DOMSubtreeModified（target是被移除节点的父节点） 

#### HTML5事件

**contextmenu：**用来添加菜单，注意要先preventDefault阻止浏览器默认的上下文菜单

**beforeunload：** 卸载页面前触发

**DOMContentLoaded：**形成完整的DOM树后就触发(load事件是页面完全加载完毕才会触发)

如果浏览器不支持，可以加载期间添加一个setTimeout(function(){},0);

**readystatechange：**支持readystatechange事件的对象都有一个readyState属性（uninitialized,loading,loaded,interactive,complete）

**pageshow与pagehide：**会话历史记录被执行时触发(前进，后退)persisted表示是否来自缓存

**hashchange：**URL参数列表（及URL中“#”后的所有字符串）发生变化时调用，必须添加给window

#### 设备事件

**orientationchange：**PC全部不支持（window.orientaition=0肖像模式，90左旋转横向模式，-90右旋转，180头朝下)

**MozOrientation：**x,y,z（0,0,1）三个属性，x表示左右倾斜（左增右减），y表示接近或远离用户倾斜（近增远减），z默认1，检测垂直加速度，移动时减小。有加速计的设备才支持事件。

**deviceorientation：**alpha(z),beta(x),gamma(y),用于定位设备的方向。absolute，表示设备是否返回一个绝对值。compassCalibrated，表示设备指南针是否校准过。有加速计的设备才支持事件

**deviceemotion：**设备是否移动。acceleration,包含xyz加速度的对象，accelerationIncludingGravity,考虑重力加速度的情况，Interval毫秒表示的时间值，rotationRate,包含alpha，beta,gamma属性的对象

#### 触摸和手势事件

**触摸事件:**touchstart.touchmove,touchend,touchcancel

除了常规属性，还包含跟踪触摸的特殊属性：

touches:表示当前跟踪触摸的Touch对象的数组，

targetTouches:特定于事件目标的Touch对象数组

changeTouches:表示自上次触摸以来发生了改变的Touch对象数组，

**触摸屏上元素时发生的事件顺序：**

touchstart -> mouseover ->mousemove(一次) -> mousedown ->mouseup -> click -> touchend

**手势事件：**gesturestart,gesturechange,gestureend

### 内存和性能：

#### ①事件委托

```js
var list = document.getElementById("myLinks");
//var item1 = document.getElementById("goSomewhere");
//var item2 = document.getElementById("doSomething");
//var item3 = document.getElementById("sayHi");
//三个item都是ul的子元素，都会冒泡，最后都会被list拿到，并且target是事件发起对象
EventUtil.addHandler(list, "click", function(event){
    event = EventUtil.getEvent(event);
    var target = EventUtil.getTarget(event);
    switch(target.id){
        case "doSomething":
            break;
        case "goSomewhere":
            break;
        case "sayHi":
            break;
    }
});
```

#### ②移除事件处理程序

移除元素之前，最好先移除元素的事件处理程序。

在事件处理程序中删除按钮也可以阻止事件冒泡，**目标元素在文档中是事件冒泡的前提**。

#### ③模拟事件

##### DOM中的事件模拟

DOM2级 UIEvents, MouseEvents, MutationEvents,HTMLEvents(DOM3级中都是单数)

DOM2级没有规定键盘事件

```js
var btn = document.getElementById("myBtn");
var event = document.createEvent("MouseEvents");
event.initMouseEvent("click",true,true,document.defaultView,0,0,0,0,0,false,false,false,false,0,null);
//type,bubbles,cancelable,view,detail,screenX,screenY,clientX,clientY,ctrlKey,altKey,shitKey,metaKey,button,relatedTarget
//触发事件
btn.dispatchEvent(event);
```

模拟键盘事件（DOM3)

```JS
var textbox = document.getElementById("myTextbox"),event;
if(document.implemntation.hasFeature("KeyboardEvents","3.0")){
    event = document.createEvent("KeyboardEvent");
    //初始化事件对象
    //DOM3级不提倡使用keypress事件
    event.initKeyboardEvent("keydown",true,true,document.defaultView,"a");
    //当前方法参数已经有变动了，以实际webDocs为准
    //type,bubbles,cancelable,view,key,loacation,modifiers,repeat
    //触发
    textbox.dispatchEvent(event);
}
```

自定义DOM事件

```js
var a = document.createEvent("CustomEvent");
a.initCustomEvent(type,bubbles,cancelable,detail);
```

##### IE中的事件模拟

```js
var event = document.createEventObject();
event.screenX = 100;
...
event.ctrlKey = false;
event.button = 0;

btn.fireEvent("onclick",event);
```

## 