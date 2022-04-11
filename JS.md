## 1.JS事件

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

#### 动画事件

animationstart->animationend,

animationiteration

#### 过渡事件

transitionend

#### 图像事件

onload,onerror

#### 滚轮事件

....

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

## 2.jQuery delegate方法

给元素的子元素添加事件处理程序

参照：输入框数字限制

```js
function limitNumber(selector) {
    selector = arguments[0]?arguments[0]:"input[type='number']";
    var reg = /[^0-9]*$/g; //edge用\d不对
    arr = [32];
    $("main").delegate(selector, 'input', function(e){
        $(this).val($(this).val().replace(reg,''));
    });
    $("main").delegate(selector, 'compositionend', function(e){
        $(this).val($(this).val().replace(reg,''));
    });
}
```

## 3.var 、let 、const 

var是函数作用域。

let和const是块级作用域。

var有变量提升，let和const没有。

```js
for(var i = 0 ; i<5; ++i){
	setTimeout(()=>console.log(i), 0); //5,5,5,5,5
}
for(let i = 0 ; i<5; ++i){
	setTimeout(()=>console.log(i), 0); //0,1,2,3,4
}
```

```js
function fn(){
   var arr = [];
   for(var i = 0;i < 5;i ++){
	 arr[i] = function(){
		 return i;
	 }
   }
   return arr;
}
var list = fn();
for(var i = 0,len = list.length;i < len ; i ++){
   console.log(list[i]());
}  // 5,5,5,5,5
```

```js
function fn(){
  var arr = [];
  for(var i = 0;i < 5;i ++){
	arr[i] = (function(i){
		return function (){
			return i;
		};
	})(i);
  }
  return arr;
}
var list = fn();
for(var i = 0,len = list.length;i < len ; i ++){
  console.log(list[i]());
}  //0,1,2,3,4
```

## 4.防抖与节流

1.防抖，事件停止一段时间后才进行事件处理

```js
function debounce(fn, delay){
    let timer = null
    return function(){
        if(timer){
            clearTimeout(timer)
        }
        timer = setTimeout(fn, delay)
    }
}
window.onscroll = debounce(fn,1000)
```

```js
function debounceAdvance(fn, delay){
    let timer
    return function() {
        clearTimeout(timer)
        timer = setTimeout(()=>{
            console.log(this, arguments)
            fn.apply(this, arguments)
        },delay)
    }
}
```

2.节流，一段时间内只执行一次事件处理函数

```js
function throttle(fn, delay){
    let valid = true
    return function(){
        if(!valid){
            return false
        }
        valid = false
        setTimeout(() => {
            fn()
            valid = true
        },delay)
    }
}
window.onscroll = throttle(fn,1000)
```

```js
function throttleAdvance(fn, delay){
    let valid = true
    return function(){
        if(!valid){
            return false
        }
        valid = false
        setTimeout(()=>{
            fn.apply(this, arguments)
            valid = true
        },delay)
    }
}
立即执行可以把fn拿到setTimeout外面
```

一般场景：

比如实时搜索框，可以节流。或者防抖

页面resize,防抖

防抖方案（Debounce）

- 搜索框搜索输入。只需用户最后一次输入完，再发送请求
- 手机号、邮箱验证输入检测
- 窗口大小Resize。只需窗口调整完成后，计算窗口大小。防止重复渲染。

节流方案（Throttle）

- 滚动加载，加载更多或滚到底部监听
- 谷歌搜索框，搜索联想功能
- 高频点击提交，表单重复提交（抢购、秒杀等）

## 5.原型，原型链

### 原型

```html
①所有引用类型都有一个__proto__(隐式原型)属性，属性值是一个普通的对象
②所有函数都有一个prototype(原型)属性，属性值是一个普通的对象
③所有引用类型的__proto__属性指向它构造函数的prototype
```

### 原型链

```html
当访问一个对象的某个属性时，会先在这个对象本身属性上查找，如果没有找到，则会去它的__proto__隐式原型上查找，即它的构造函数的prototype，如果还没有找到就会再在构造函数的prototype的__proto__中查找，这样一层一层向上查找就会形成一个链式结构，我们称为原型链。
```

### js中如何继承

#### 1、属性继承

```js
function Person (name, age) {
    this.name = name
    this.age = age
}

// 方法定义在构造函数的原型上
Person.prototype.getName = function () { console.log(this.name)}

function Teacher (name, age, subject) {
    Person.call(this, name, age)
    this.subject = subject
}
```

**属性的继承是通过在一个类内执行另外一个类的构造函数，通过`call`指定`this`为当前执行环境，这样就可以得到另外一个类的所有属性。**

#### 2、方法继承

```js
Teacher.prototype = Object.create(Person.prototype)
Teacher.prototype.constructor = Teacher
```

**[Object.create](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)简单说就是新建一个对象，使用现有的对象赋值给新建对象的`__proto__`**

#### 3、hasOwnProperty

**在原型链上查询属性比较耗时，对性能有影响，试图访问不存在的属性时会遍历整个原型链。**

**遍历对象属性时，每个可枚举的属性都会被枚举出来。 要检查是否具有自己定义的属性，而不是原型链上的属性，必须使用hasOwnProperty方法。**

**hasOwnProperty 是 JavaScript 中唯一处理属性并且不会遍历原型链的方法。**

#### 4、instanceof ,valueOf(), typeof 

typeof 返回的是类型的字符串值

`valueOf()`函数用于返回指定对象的原始值。该方法属于`Object`对象，由于所有的对象都"继承"了Object的对象实例，因此几乎所有的实例对象都可以使用该方法。所有主流浏览器均支持该函数。

`instanceof` 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。

## 6.Promise

promise是一个对象，对象和函数的区别就是对象可以保存状态，函数不可以（闭包除外）

并未剥夺函数return的能力，因此无需层层传递callback，进行回调获取数据

代码风格，容易理解，便于维护

多个异步等待合并便于解决

https://zhuanlan.zhihu.com/p/144058361

```js
new Promise(
  function (resolve, reject) {
    // 一段耗时的异步操作
    resolve('成功') // 数据处理完成
    // reject('失败') // 数据处理出错
  }
).then(
  (res) => {console.log(res)},  // 成功
  (err) => {console.log(err)} // 失败
)
```

#### 手写一个promise

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
function Promise(executor) {
    var _this = this
    this.state = PENDING; //状态
    this.value = undefined; //成功结果
    this.reason = undefined; //失败原因

    this.onFulfilled = [];//成功的回调
    this.onRejected = []; //失败的回调
    function resolve(value) {
        if(_this.state === PENDING){
            _this.state = FULFILLED
            _this.value = value
            _this.onFulfilled.forEach(fn => fn(value))
        }
    }
    function reject(reason) {
        if(_this.state === PENDING){
            _this.state = REJECTED
            _this.reason = reason
            _this.onRejected.forEach(fn => fn(reason))
        }
    }
    try {
        executor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}
Promise.prototype.then = function (onFulfilled, onRejected) {
    if(this.state === FULFILLED){
        typeof onFulfilled === 'function' && onFulfilled(this.value)
    }
    if(this.state === REJECTED){
        typeof onRejected === 'function' && onRejected(this.reason)
    }
    if(this.state === PENDING){
        typeof onFulfilled === 'function' && this.onFulfilled.push(onFulfilled)
        typeof onRejected === 'function' && this.onRejected.push(onRejected)
    }
};
```

...待完成

手写一个timeout函数

## 7.正则表达式

str.search()返回子串的起始位置

str.replace(Reg,'adasd')

i大小写不敏感,g全局匹配,m多行匹配,

[abc],[0-9],(x|y)

\d \s \b \w \uxxxx n+ n* n?

...待完成

## 8.柯里化，手写

9.JSON.stringify

10.JS的隐式转换

<<<<<<< Updated upstream
11.call,apply,bind的区别

https://www.bilibili.com/read/cv6233347

bind返回的是原函数的一个拷贝

```js
const $ = document.querySelector.bind(document)
```

深入理解bind

https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/

默认情况下[`setTimeout()`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout)，`this`关键字将设置为 [`window`](https://developer.mozilla.org/en-US/docs/Web/API/Window)（或`global`）对象。当使用需要`this`引用类实例的类方法时，您可以显式绑定 `this`到回调函数，以维护实例。



12.类

https://www.bilibili.com/read/cv13110696

13.一些object.prototype方法的手写

https://www.bilibili.com/read/cv12484729?from=articleDetail

14.array.prototype.flat

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/flat



15.CommonJs模块化和ESModule模块化

CommonJs中使用require()加载和module.exports输出

```js
const A  = require()
const { a, b, c} = require() //相当于解构赋值
// require相当于把require的模块当作大对象整体引入
// 无法做treeShaking进行优化
```

```js
exports.A = function(){}
module.exports = {}
//exports实际上是一个指向module.exports的变量， 
//如果直接赋值exports只是会让exports不指向module.exports,并不会对导出有影响
```

ES6模块使用import和export

```
export default xxxx
对应import A from xxx 或 import {default as A} from xxx

export A 
export B
对应import {A,B} from xxx
```



16.监听动画结束

animationend事件

`animationend` 事件会在一个 CSS 动画完成时触发（不包括完成前就已终止的情况，例如元素变得不可见或者动画从元素中移除）。



同类事件有

animationstart

`animationstart` 事件会在 CSS 动画开始时触发。 如果有 `animation-delay` 延时，事件会在延迟时效过后立即触发。为负数的延时时长会致使事件被触发时事件的 `elapsedTime` 属性值等于该时长的绝对值（并且，相应地，动画将直接播放该时长绝对值之后的动画）。



**`animationiteration`** 事件将被触发 当[CSS 动画](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations)的迭代结束且另一个迭代开始时。此事件不会与 [`animationend`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/animationend_event) 事件同时发生t, 因此对于`animation-iteration-count`次数为1的动画不会发生。



一个 `**animationcancel**` 事件会在一个 [CSS Animation](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations) 意外终止时触发. 换句话说, 就是任意时刻 CSS Animation 在没有发送 `animationend (en-US)` 事件时停止运行. 这种情况会在  [`animation-name`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-name) 发生改变导致动画被移除时, 或者使用CSS隐藏了动画中的node节点. 因此要么node节点直接被隐藏，要么因为node节点的父节点被隐藏.



17.RequestAnimationFrame和RequestIdleCallback

https://www.cnblogs.com/cangqinglang/p/13877078.html



18.div绑定键盘监听事件，

div需要加tabIndex属性才能让整个div监听到键盘事件，否则只有在div中可以focus的位置比如input,textarea,button的位置才能监听到keydown
=======
## 9.MVC，MVP,MVVM

#### 面向过程

```js
let btn = document.getElementsByClassName("btn")[0];
let boxDom = document.getElementsByClassName("box")[0];
let flag = true;
btn.onclick = function(){
    if(flag){
        boxDom.style.display = "none";
        flag = false;
    }else{
        boxDom.style.display = "block";
        flag = true;
    }
}
```

#### MVC写法

![img](https://www.pianshen.com/images/852/7f4f8b6e9027262bfb0a80fd03737574.png)

![img](https://www.pianshen.com/images/329/bb19d7a8e53a070a126faf0bf2973801.png)

```js
 //view
let boxDom = document.getElementsByClassName("box")[0];
//model
let model = {
    isShow:true,
    isBig:false
}

//controller 业务逻辑
function Controller(){
    this.init();//初始化
}
Controller.prototype = {
    constructor:Controller,
    init:function(){
        this.addEvent()
    },
    addEvent:function(){
        let btn = document.getElementsByClassName("btn")[0];
        let btn2 = document.getElementsByClassName("btn2")[0];
        let that = this;
        btn.onclick = function(){
            model.isShow = !model.isShow;
            //更改视图了
            that.render();
        }
        btn2.onclick = function(){
            model.isBig = true;
            //更改视图了
            that.render();
        }
    },
    render:function(){//数据驱动视图的更改
        boxDom.style.display = model.isShow?"block":"none";
        boxDom.style.width = model.isBig?"300px":"100px";
    }
}

new Controller();//初始化一下
```

#### MVP

![img](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015020109.png)

MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。

1. 各部分之间的通信，都是双向的。

2. View 与 Model 不发生联系，都通过 Presenter 传递。

3. View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里

#### MVVM

MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。

唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。

m:model数据模型层  v:view视图层  vm:ViewModel
vue中采用的是mvvm模式，这是从mvc衍生过来的
MVVM让视图与viewmodel直接的关系特别的紧密，就是为了解决mvc反馈不及时的问题  

图片说明一下：

![8ef6d787d9ac6382443763424e92516e.png](https://www.pianshen.com/images/654/8ef6d787d9ac6382443763424e92516e.png)

双向绑定的原理是什么：

（当视图改变的时候更新模型层，当模型层改变的时候更新视图层）

vue在创建vm的时候，会将数据配置在实例当中，然后会使用Object.defineProperty对这些数据进行处理，为这些数据添加getter与setter方法。当获取数据的时候，会触发对应的getter方法，当设置数据的时候，会触发对应的setter方法，从而进一步触发vm上的watcher方法，然后数据了，vm进一步去更新视图。

**严格的MVVM要求View不能和Model直接通信，而Vue在组件提供了$refs这个属性，让Model可以直接操作View，违反了这一规定，所以说Vue没有完全遵循MVVM。**

MVVM只能数据驱动视图，视图更改数据，而不能通过其他方式操作数据。

Model变更触发View更新必须通过VewModel (Vue实例)。----- set时触发依赖，异步更新模板

而View变更后触发Model也必须通过VIewMode。 ---------利用v-model指令 ， 用户手动输入数据  触发@input事件，更改数据。

以上是MVVM的思想。

>>>>>>> Stashed changes

