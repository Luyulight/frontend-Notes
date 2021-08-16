

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
"0o"转换为8进制对应的10进制数值
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

```js
let sym1 = Symbol.for('foo')
let sym2 = Symbol.for('foo')
let sym3 = Symbol('foo')
sym2 ===sym1 //true
sym2 === sym3 //false
```

```js
let s = Symbol.for('foo')
console.log(Symbol.keyFor(s)) //foo
Symbol.keyFor(123) //TypeError:123 is not a symbol
```

##### ③使用符号作为属性

```js
let s1 = Symbol('foo')
let s2 = Symbol('bar')
let o = {
	[s1]:'foo val'
}
//或者 o[s1]='foo bar'
Object.defineProperty(o,s2,{value:'bar val'})
console.log(o) //{Symbol(foo):foo val,Symbol(bar):bar val}
//或者 Object.defineProperties{o,{
	[s1]:{value:'foo val'},
	[s2]:{value:'bar val'}
}}
```

```js
let o ={
	[s1]:{value:'foo val'},
	[s2]:{value:'bar val'},
	baz:'baz val',
	qux:'qux val'
}
Object.getOwnPropertySymbols(o) //[Symbol(foo),Symbol(bar)]
Object.getOwnPropertyNames(o)	//["baz","quz"]
Object.getOwnPropertyDescriptors(o) //{baz:{...},quz:{...},Symbol(foo):{...},Symbol(bar):{...}}
Reflect.ownKeys(o) //["baz","quz",Symbol(foo),Symbol(bar)]
```

符号属性是对内存中符号的一个引用，直接创建用作属性的符号不会丢失。但是如果没有显示地保存对这些属性的引用，必须遍历对象所有的符号属性才能找到相应的属性键（因为Symbol(foo)不等于Symbol(foo))

```js
let o = {
	Symbol('foo'):{value:'foo val'},
	Symbol('bar'):{value:'bar val'}
}
console.log(o) //{Symbol(foo):foo val,Symbol(bar):bar val}
let barSymbol = Object.getOwnPropertySymbols(o).find((symbol)=>symbol.toString().match(/bar/))
console.log(barSymbol) //Symbol(bar)
```

##### ④常用内置符号

ES6引入了一批常用内置符号，用于暴露语言内部行为，这些内置符号都以Symbol工厂函数字符串属性的形式存在

重要用途之一就是访问并重新定义他们，从而改变原生结构的行为。

内置符号就是全局函数Symbol的普通字符串属性。所有内置符号属性都是不可写不可枚举不可配置的

```js
let a = Symbol
console.log(Reflect.ownKeys(a))
//["length", "name", "prototype", "for", "keyFor", "asyncIterator", "hasInstance", "isConcatSpreadable", "iterator", "match", "matchAll", "replace", "search", "species", "split", "toPrimitive", "toStringTag", "unscopables"]
```

一般引用这些符号名称，可以用@@形式，比如@@iterator指的就是Symbol.iterator(仅描述，不用于代码)

##### ⑤Symbol.asyncIterator

返回对象默认的AsyncIterator,表示实现异步迭代器API的函数

```js
class Emitter{
	constructor(max) {
		this.max = max
		this.asyncIdx = 0
	}
	async *[Symbol.asyncIterator](){
		while(this.asyncIdx < this.max){
			yield new Promise((resolve)=>resolve(this.asyncIdx++))
		}
	}
}
async function asyncCount(){
	let emitter = new Emitter(5)
	for await(const x of emitter){
		console.log(x)
	}
}
asyncCount()
//0
//1
//2
//3
//4
```

##### ⑥Symbol.hasInstance

instanceof操作符可以用来确定一个对象实例的原型链上是否有原型

```js
function Foo(){}
let f = new Foo()
console.log(f instanceof Foo)//true
```

ES6中instanceof操作符会使用Symbol.hasInstance函数来确定关系

```
function Foo(){}
let f = new Foo()
console.log(Foo[Symbol.hasInstance](f))//true
```

##### ⑦Symbol.isConcatSpreadable

false或假值会阻止concat将加入的数组对象（数组或者数组对象）打平追加到数组中，而是保留原对象

而其他不是类数组的对象，在true的情况下会被忽略

```js
let initial = ['foo']; 
let array = ['bar']; 
console.log(array[Symbol.isConcatSpreadable]); // undefined 
console.log(initial.concat(array)); // ['foo', 'bar'] 
array[Symbol.isConcatSpreadable] = false; 
console.log(initial.concat(array)); // ['foo', Array(1)]

let arrayLikeObject = { length: 1, 0: 'baz' }; 
console.log(arrayLikeObject[Symbol.isConcatSpreadable]); // undefined 
console.log(initial.concat(arrayLikeObject)); // ['foo', {...}] 
arrayLikeObject[Symbol.isConcatSpreadable] = true; 
console.log(initial.concat(arrayLikeObject)); // ['foo', 'baz'] 

let otherObject = new Set().add('qux'); 
console.log(otherObject[Symbol.isConcatSpreadable]); // undefined 
console.log(initial.concat(otherObject)); // ['foo', Set(1)] 
otherObject[Symbol.isConcatSpreadable] = true; 
console.log(initial.concat(otherObject)); // ['foo']
```

##### ⑧Symbol.iterator

```js
class Foo { 
 *[Symbol.iterator]() {} 
} 
let f = new Foo(); 
console.log(f[Symbol.iterator]()); 
// Generator {<suspended>} 
```

```js
 constructor(max) { 
 this.max = max; 
 this.idx = 0; 
 } 
 *[Symbol.iterator]() { 
     while(this.idx < this.max) { 
        yield this.idx++; 
     } 
 } 
} 
function count() { 
 let emitter = new Emitter(5); 
 for (const x of emitter) { 
 console.log(x); 
 } 
} 
count(); 
// 0
// 1 
// 2 
// 3 
// 4
```

##### ⑨Symbol.match

这个符号作为“一个正则表达式方法，该方法用正则去匹配字符串。由String.prototype.match()方法使用”

String.prototype.match()方法会使
用以Symbol.match 为键的函数来对正则表达式求值。正则表达式的原型上默认有这个函数的定义，因此所有正则表达式实例默认是这个String 方法的有效参数

```js
console.log(RegExp.prototype[Symbol.match]);
// ƒ [Symbol.match]() { [native code] }
console.log('foobar'.match(/bar/));
// ["bar", index: 3, input: "foobar", groups: undefined]
```

给这个方法传入非正则表达式值会导致该值被转换为RegExp 对象。如果想改变这种行为，让方法直接使用参数，则可以重新定义Symbol.match 函数以取代默认对正则表达式求值的行为，从而让match()方法使用非正则表达式实例。Symbol.match 函数接收一个参数，就是调用match()方法的字符串实例。返回的值没有限制：

```js
class FooMatcher {
    static [Symbol.match](target) {
    	return target.includes('foo');
    }
}
console.log('foobar'.match(FooMatcher)); // true
console.log('barbaz'.match(FooMatcher)); // false
class StringMatcher {
    constructor(str) {
    	this.str = str;
    }
	[Symbol.match](target) {
		return target.includes(this.str);
	}
}
console.log('foobar'.match(new StringMatcher('foo'))); // true
console.log('barbaz'.match(new StringMatcher('qux'))); // false
```

##### ⑩Symbol.replace

介绍同Symbol.match

```js
console.log(RegExp.prototype[Symbol.replace]);
// ƒ [Symbol.replace]() { [native code] }
console.log('foobarbaz'.replace(/bar/, 'qux'));
// 'fooquxbaz'
class FooReplacer {
    static [Symbol.replace](target, replacement) {
    	return target.split('foo').join(replacement);
    }
}
console.log('barfoobaz'.replace(FooReplacer, 'qux'));
// "barquxbaz"
class StringReplacer {
    constructor(str) {
    	this.str = str;
    }
    [Symbol.replace](target, replacement) {
    	return target.split(this.str).join(replacement);
    }
}
console.log('barfoobaz'.replace(new StringReplacer('foo'), 'qux'));
// "barquxbaz"
```

##### ①①Symbol.search

介绍同Symbol.match

```js
console.log(RegExp.prototype[Symbol.search]);
// ƒ [Symbol.search]() { [native code] }
console.log('foobar'.search(/bar/));
// 3
class FooSearcher {
    static [Symbol.search](target) {
    	return target.indexOf('foo');
    }
}
 console.log('foobar'.search(FooSearcher)); // 0
console.log('barfoo'.search(FooSearcher)); // 3
console.log('barbaz'.search(FooSearcher)); // -1
class StringSearcher {
    constructor(str) {
    	this.str = str;
    }
    [Symbol.search](target) {
    	return target.indexOf(this.str);
    }
}
console.log('foobar'.search(new StringSearcher('foo'))); // 0
console.log('barfoo'.search(new StringSearcher('foo'))); // 3
console.log('barbaz'.search(new StringSearcher('qux'))); // -1
```

##### ①②Symbol.species

这个符号作为一个属性表示“一个函数值，该函数作为创建派生对象的构造函数”。这个属性在内置类型中最常用，用于对内置类型实例方法的返回值暴露实例化派生对象的方法。用Symbol.species 定义静态的获取器（getter）方法，可以覆盖新创建实例的原型定义：

```js
class Bar extends Array {}
class Baz extends Array {
    static get [Symbol.species]() {
    	return Array;
    }
}
let bar = new Bar();
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true
bar = bar.concat('bar');
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true

let baz = new Baz(); // Baz[]
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); // true
baz = baz.concat('baz'); //['bsx'] 丢失了原来的Baz原型
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); // false

class Bat extends Array {
    static get [Symbol.species]() {
    	return Bat; //!!
    }
}
let bat = new Bat(); // Bat[]
console.log(bat instanceof Array); // true
console.log(bat instanceof Bat); // true
bat = bat.concat('bat'); //Bat['bat'] 
console.log(bat instanceof Array); // true
console.log(bat instanceof Bat); // true
```

##### ①③Symbol.split

介绍同Symbol.match

```js
console.log(RegExp.prototype[Symbol.split]);
// ƒ [Symbol.split]() { [native code] }
console.log('foobarbaz'.split(/bar/));
// ['foo', 'baz']
class FooSplitter {
    static [Symbol.split](target) {
    	return target.split('foo');
    }
}
console.log('barfoobaz'.split(FooSplitter));
// ["bar", "baz"]
class StringSplitter {
    constructor(str) {
    	this.str = str;
    }
    [Symbol.split](target) {
    	return target.split(this.str);
    }
}
console.log('barfoobaz'.split(new StringSplitter('foo')));
// ["bar", "baz"]
```

##### ①④Symbol.toPrimitive

​		根据ECMAScript 规范，这个符号作为一个属性表示“一个方法，该方法将对象转换为相应的原始值。由ToPrimitive 抽象操作使用”。很多内置操作都会尝试强制将对象转换为原始值，包括字符串、
​		数值和未指定的原始类型。对于一个自定义对象实例，通过在这个实例的Symbol.toPrimitive 属性上定义一个函数可以改变默认行为。根据提供给这个函数的参数（string、number 或default），可以控制返回的原始值：

```js
class Foo {}
let foo = new Foo();
console.log(3 + foo); // "3[object Object]"
console.log(3 - foo); // NaN
console.log(String(foo)); // "[object Object]"
class Bar {
    constructor() {
        this[Symbol.toPrimitive] = function(hint) {
            switch (hint) {
            	case 'number':
            		return 3;
            	case 'string':
            		return 'string bar';
            	case 'default':
            	default:
            		return 'default bar';
            }
        }
    }
}
```

##### ①⑤Symbol.toStringTag

根据ECMAScript 规范，这个符号作为一个属性表示“一个字符串，该字符串用于创建对象的默认字符串描述。由内置方法Object.prototype.toString()使用”。
通过toString()方法获取对象标识时，会检索由Symbol.toStringTag 指定的实例标识符，默认为"Object"。内置类型已经指定了这个值，但自定义类实例还需要明确定义：

```js
let s = new Set();
console.log(s); // Set(0) {}
console.log(s.toString()); // [object Set]
console.log(s[Symbol.toStringTag]); // Set
class Foo {}
let foo = new Foo();
console.log(foo); // Foo {}
console.log(foo.toString()); // [object Object]
console.log(foo[Symbol.toStringTag]); // undefined
class Bar {
    constructor() {
    	this[Symbol.toStringTag] = 'Bar';
    }
}
let bar = new Bar();
console.log(bar); // Bar {}
console.log(bar.toString()); // [object Bar]
console.log(bar[Symbol.toStringTag]); // Bar
```

##### ①⑥Symbol.unscopables

根据ECMAScript 规范，这个符号作为一个属性表示“一个对象，该对象所有的以及继承的属性，都会从关联对象的with 环境绑定中排除”。设置这个符号并让其映射对应属性的键值为true，就可以阻止该属性出现在with 环境绑定中

```js
let o = { foo: 'bar' };
with (o) {
	console.log(foo); // bar
}
o[Symbol.unscopables] = {
	foo: true
};
with (o) {
	console.log(foo); // ReferenceError
}
```

### 复杂类型

#### Object

```js
每个Object 实例都有如下属性和方法。
constructor
//用于创建当前对象的函数。在前面的例子中，这个属性的值就是Object()函数。
hasOwnProperty(propertyName)
//用于判断当前对象实例（不是原型）上是否存在给定的属性。要检查的属性名必须是字符串（如o.hasOwnProperty("name")）或符号。
isPrototypeOf(object)
//用于判断当前对象是否为另一个对象的原型。
propertyIsEnumerable(propertyName)
//用于判断给定的属性是否可以使用。与hasOwnProperty()一样，属性名必须是字符串。
toLocaleString()
//返回对象的字符串表示，该字符串反映对象所在的本地化执行环境。
toString()
//返回对象的字符串表示。
valueOf()
//返回对象对应的字符串、数值或布尔值表示。通常与toString()的返回值相同。
```

## 3.5操作符

### 3.5.1一元操作符

#### ++ --

```js
let num1 = 2;
let num2 = 20;
let num3 = --num1 + num2;
let num4 = num1 + num2;
console.log(num3); // 21
console.log(num4); // 21
```

```js
let num1 = 2;
let num2 = 20;
let num3 = num1-- + num2;
let num4 = num1 + num2;
console.log(num3); // 22
console.log(num4); // 21
```

```
递增和递减操作符遵循如下规则。
 对于字符串，如果是有效的数值形式，则转换为数值再应用改变。变量类型从字符串变成数值。
 对于字符串，如果不是有效的数值形式，则将变量的值设置为NaN 。变量类型从字符串变成
数值。
 对于布尔值，如果是false，则转换为0 再应用改变。变量类型从布尔值变成数值。
 对于布尔值，如果是true，则转换为1 再应用改变。变量类型从布尔值变成数值。
 对于浮点值，加1 或减1。
 如果是对象，则调用其（第5 章会详细介绍的）valueOf()方法取得可以操作的值。对得到的
值应用上述规则。如果是NaN，则调用toString()并再次应用其他规则。变量类型从对象变成
数值
```

```js
let s1 = "2";
let s2 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
    	return -1;
    }
};
s1++; // 值变成数值3
s2++; // 值变成NaN
b++; // 值变成数值1
f--; // 值变成0.10000000000000009（因为浮点数不精确）
o--; // 值变成-2
```

#### 一元+ -

如果将一元加应用到非数值，则会执行与使用Number()转型函数一样的类型转换：布尔值false
和true 转换为0 和1，字符串根据特殊规则进行解析，对象会调用它们的valueOf()和/或toString()
方法以得到可以转换的值。

对数值使用一元减会将其变成相应的负值（如上面的例子所示）。在应用到非数值时，一元减会遵
循与一元加同样的规则，先对它们进行转换，然后再取负值：

```js
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let f = 1.1;
let o = {
    valueOf() {
    	return -1;
    }
};
if(+){
	s1 = +s1; // 值变成数值1
    s2 = +s2; // 值变成数值1.1
    s3 = +s3; // 值变成NaN
    b = +b; // 值变成数值0
    f = +f; // 不变，还是1.1
    o = +o; // 值变成数值-1
}else{
    s1 = -s1; // 值变成数值-1
    s2 = -s2; // 值变成数值-1.1
    s3 = -s3; // 值变成NaN
    b = -b; // 值变成数值0
    f = -f; // 变成-1.1
    o = -o; // 值变成数值1
}
```

### 3.5.2 位操作符

ECMAScript中的所有数值都以IEEE 754 64 位格式存储，但位操作并不直接应用到64 位表示，而是先把值转换为32 位整数，再进行位操作，之后再把结果转换为64 位。对开发者而言，只需要考虑32 位整数即可。

负数是补码形式存储，绝对值二进制->取反码->+1

**在32和64位之间转换时，NaN和Infinity会被当成0处理**

位操作符应用到非数值，那么首先会使用Number()函数将该值转换为数值（这个过程是自动的），然后再应用位操作。最终结果是数值。

按位非~       按位与&     按位或|      按位异或^       

按位左移<<（空位补0）

按位右移>> （左空位补符号值 正0,负1）

无符号右移>>> （左补0）

### 3.5.3布尔操作符

#### 逻辑非 !

```
 如果操作数是对象，则返回false。
 如果操作数是空字符串，则返回true。
 如果操作数是非空字符串，则返回false。
 如果操作数是数值0，则返回true。
 如果操作数是非0 数值（包括Infinity），则返回false。
 如果操作数是null，则返回true。
 如果操作数是NaN，则返回true。
 如果操作数是undefined，则返回true。
```

#### 逻辑与 &&

```
 如果第一个操作数是对象，则返回第二个操作数。
 如果第二个操作数是对象，则只有第一个操作数求值为true 才会返回该对象。
 如果两个操作数都是对象，则返回第二个操作数。
 如果有一个操作数是null，则返回null。
 如果有一个操作数是NaN，则返回NaN。
 如果有一个操作数是undefined，则返回undefined。
如果是null && NaN && undefined 则按左右顺序返回！！第一个值！！
```

逻辑与操作符是一种短路操作符，意思就是如果第一个操作数false，那么永远不会对第二个
操作数求值（即使第二个操作数未定义或者执行报错）。

#### 逻辑或 ||

```
 如果第一个操作数是对象，则返回第一个操作数。
 如果第一个操作数求值为false，则返回第二个操作数。
 如果两个操作数都是对象，则返回第一个操作数。
 如果两个操作数都是null，则返回null。
 如果两个操作数都是NaN，则返回NaN。
 如果两个操作数都是undefined，则返回undefined。
如果是null || NaN || undefined 则按左右顺序返回！！最后一个值！！
```

逻辑或操作符是一种短路操作符，意思就是如果第一个操作数true，那么永远不会对第二个
操作数求值。

=>赋值的简写

```js
let myObject = preferredObject || backupObject;
```

### 3.5.4乘性操作符

操作数如果不是Number会被Number()转换（空字符串是0）

#### 乘法*

```
 如果操作数都是数值，则执行常规的乘法运算，即两个正值相乘是正值，两个负值相乘也是正值，正负符号不同的值相乘得到负值。如果ECMAScript 不能表示乘积，则返回Infinity 或-Infinity。
 如果是Infinity 乘以0，则返回NaN。
 如果是Infinity 乘以非0 的有限数值，则根据第二个操作数的符号返回Infinity 或-Infinity。
 如果是Infinity 乘以Infinity，则返回Infinity。
 如果有不是数值的操作数，则先在后台用Number()将其转换为数值，然后再应用上述规则。
 如果有任一操作数是NaN，则返回NaN。
```

#### 除法/

```
 如果操作数都是数值，则执行常规的除法运算，即两个正值相除是正值，两个负值相除也是正值，符号不同的值相除得到负值。如果ECMAScript不能表示商，则返回Infinity 或-Infinity。
 如果有任一操作数是NaN，则返回NaN。
 如果是Infinity 除以Infinity，则返回NaN。
 如果是0 除以0，则返回NaN。
 如果是非0 的有限值除以0，则根据第一个操作数的符号返回Infinity 或-Infinity。
 如果是Infinity 除以任何数值，则根据第二个操作数的符号返回Infinity 或-Infinity。
 如果有不是数值的操作数，则先在后台用Number()函数将其转换为数值，然后再应用上述规则。
```

#### 取模%

```
 如果操作数是数值，则执行常规除法运算，返回余数。
 如果被除数是无限值，除数是有限值，则返回NaN。
 如果被除数是有限值，除数是0，则返回NaN。
 如果是Infinity 除以Infinity，则返回NaN。
 如果被除数是有限值，除数是无限值，则返回被除数。
 如果被除数是0，除数不是0，则返回0。
 如果有不是数值的操作数，则先在后台用Number()函数将其转换为数值，然后再应用上述规则。
```

### 3.5.5指数操作符

Math.pow(a,b)  

ES7 => a**b

**指数赋值**

```js
let a = 3; a **=2 
console.log(a) //9
let b = 16; b**=0.5
console.log(b) //4
```

### 3.5.6加性操作符

#### 加法+

如果两个操作数都是数值，加法操作符执行加法运算并根据如下规则返回结果：

```
 如果有任一操作数是NaN，则返回NaN；
 如果是Infinity 加Infinity，则返回Infinity；
 如果是-Infinity 加-Infinity，则返回-Infinity；
 如果是Infinity 加-Infinity，则返回NaN；
 如果是+0 加+0，则返回+0；
 如果是-0 加+0，则返回+0；
 如果是-0 加-0，则返回-0。
```

不过，如果有一个操作数是字符串，则要应用如下规则：

```
 如果两个操作数都是字符串，则将第二个字符串拼接到第一个字符串后面；
 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，再将两个字符串拼接在一起。
```

**如果有任一操作数是对象、数值或布尔值，则调用它们的toString()方法以获取字符串，然后再应用前面的关于字符串的规则。**

**对于undefined 和null，则调用String()函数，分别获取"undefined"和"null"。**

```js
let result1 = 5 + 5; // 两个数值
console.log(result1); // 10
let result2 = 5 + "5"; // 一个数值和一个字符串
console.log(result2); // "55"
```

```js
let num1 = 5;
let num2 = 10;
let message = "The sum of 5 and 10 is " + num1 + num2;
console.log(message); // "The sum of 5 and 10 is 510"
let num1 = 5;
let num2 = 10;
let message = "The sum of 5 and 10 is " + (num1 + num2);
console.log(message); // "The sum of 5 and 10 is 15"
```

#### 减法-

```
 如果两个操作数都是数值，则执行数学减法运算并返回结果。
 如果有任一操作数是NaN，则返回NaN。
 如果是Infinity 减Infinity，则返回NaN。
 如果是-Infinity 减-Infinity，则返回NaN。
 如果是Infinity 减-Infinity，则返回Infinity。
 如果是-Infinity 减Infinity，则返回-Infinity。
 如果是+0 减+0，则返回+0。
 如果是+0 减-0，则返回-0。
 如果是-0 减-0，则返回+0。
 如果有任一操作数是字符串、布尔值、null 或undefined，则先在后台使用Number()将其转换为数值，然后再根据前面的规则执行数学运算。如果转换结果是NaN，则减法计算的结果是NaN。
(null转换0，undefined转换NaN)
 如果有任一操作数是对象，则调用其valueOf()方法取得表示它的数值。如果该值是NaN，则减法计算的结果是NaN。如果对象没有valueOf()方法，则调用其toString()方法，然后再将得到的字符串转换为数值。
```

### 3.5.7关系操作符

```
>、<、<=、>=     都返回布尔值
```

```
 如果操作数都是数值，则执行数值比较。
 如果操作数都是字符串，则逐个比较字符串中对应字符的编码。
 如果有任一操作数是数值，则将另一个操作数转换为数值，执行数值比较。
 如果有任一操作数是对象，则调用其valueOf()方法，取得结果后再根据前面的规则执行比较。如果没有valueOf()操作符，则调用toString()方法，取得结果后再根据前面的规则执行比较。
 如果有任一操作数是布尔值，则将其转换为数值再执行比较。
```

```js 
let result = "Brick" < "alphabet"; // true
let result = "Brick".toLowerCase() < "alphabet".toLowerCase(); // false
let result = "23" < "3"; // true
let result = "23" < 3; // false
let result = "a" < 3; // 因为"a"会转换为NaN，所以结果是false
let result1 = NaN < 3; // false
let result2 = NaN >= 3; // false
```

### 3.5.8相等操作符

#### 等于==和不等于!=

这两个操作符都会先进行**类型转换**（通常称为强制类型转换）再确定操作数是否相等。

```
 如果任一操作数是布尔值，则将其转换为数值再比较是否相等。false 转换为0，true 转换为1。
 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等。
 如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf()方法取得其原始值，再根据前面的规则进行比较。
```

在进行比较时，这两个操作符会遵循如下规则。

```
 null 和undefined 相等。
 null 和undefined 不能转换为其他类型的值再进行比较。
 如果有任一操作数是NaN，则相等操作符返回false，不相等操作符返回true。记住：即使两个操作数都是NaN，相等操作符也返回false，因为按照规则，NaN 不等于NaN。
 如果两个操作数都是对象，则比较它们是不是！同一个对象！。如果两个操作数都指向同一个对象，则相等操作符返回true。否则，两者不相等。
```

```js
 null == undefined //true
"NaN" == NaN       //"NaN"被Number转换为NaN,NaN不等于NaN false
5 == NaN           //false
NaN == NaN         //false
NaN != NaN         //true
false == 0         //true
true == 1          //true
true == 2          //false
undefined == 0     //false
null == 0          //false
"5" == 5           //true
```

#### 全等===和不全等!==

不转换的情况下相等

对象得是指向同一个对象，全等

### 3.5.9条件操作符

```js
variable = boolean_expression ? true_value : false_value;
```

### 3.5.10赋值操作符

```
每个数学操作符以及其他一些操作符都有对应的复合赋值操作符：
 乘后赋值（*=）
 除后赋值（/=）
 取模后赋值（%=）
 加后赋值（+=）
 减后赋值（-=）
 左移后赋值（<<=）
 右移后赋值（>>=）
 无符号右移后赋值（>>>=）
这些操作符仅仅是简写语法，使用它们不会提升性能
```

### 3.5.11逗号操作符

```js
let num1 = 1, num2 = 2, num3 = 3;
let num = (5, 1, 4, 8, 0); // num 的值为0
```

## 3.6语句

### 3.6.1 if-else if-else

```js
if (condition1) statement1 else if (condition2) statement2 else statement3
```

### 3.6.2 do-while

do-while 语句是一种后测试循环语句，即循环体中的代码执行后才会对退出条件进行求值。换句话说，循环体内的代码**至少执行一次**。

```js
do {
	statement
} while (expression);
```

### 3.6.3 while

while 语句是一种先测试循环语句，即先检测退出条件，再执行循环体内的代码。因此，while 循环体内的代码有**可能不会执行**。

```js
while(expression) statement
```

### 3.6.4 for

for 语句也是先测试语句，只不过增加了进入循环之前的初始化代码，以及循环执行后要执行的表达式

无法通过while 循环实现的逻辑，同样也无法使用for 循环实现。因此for 循环只是将循环相关的代码封装在了一起而已

```js 
for (initialization; expression; post-loop-expression) statement
```

```js
//无限循环
for(;;){
	doSomething()
}
```

### 3.6.5 for-in

for-in 语句是一种严格的迭代语句，用于枚举对象中的**非符号键**属性

```js
for (propertyName in expression) statement
```

ECMAScript 中对象的属性是无序的，因此for-in 语句不能保证返回对象属性的顺序。换句话说，所有可枚举的属性都会返回一次，但返回的顺序可能会因浏览器而异。

如果for-in 循环要迭代的变量是null 或undefined，则不执行循环体。

### 3.6.6 for-of

for-of 语句是一种严格的迭代语句，用于遍历可迭代对象的元素

```js
for (property of expression) statement
```

在这个例子中，我们使用for-of 语句显示了一个包含4 个元素的数组中的所有元素。循环会一直持续到将所有元素都迭代完。与for 循环一样，这里控制语句中的const 也不是必需的。但为了确保这个局部变量不被修改，推荐使用const。
for-of 循环会**按照可迭代对象的next()方法产生值的顺序**迭代元素。

ES2018 对for-of 语句进行了扩展，增加了for-await-of 循环，以支持生成期约（promise）的异步可迭代对象。

### 3.6.7 标签语句

```js
label: statement
```

可以在后面通过break 或continue 语句引用。标签语句的典型应用场景是嵌套循环。

### 3.6.8 break和continue

break 和continue 语句为执行循环代码提供了更严格的控制手段。其中，break 语句用于立即退出循环，强制执行循环后的下一条语句。而continue 语句也用于立即退出循环，但会再次从循环顶部开始执行。

break 和continue 都可以与标签语句一起使用，返回代码中特定的位置

### 3.6.9 with

with 语句的用途是将代码作用域设置为特定的对象

```js
with (expression) statement;
```

```js
with(location) {
    let qs = search.substring(1);
    let hostName = hostname;
    let url = href;
}
// 可以视作等价为
let qs = location.search.substring(1);
let hostName = location.hostname;
let url = location.href;
```

这里，with 语句用于连接location 对象。这意味着在这个语句内部，每个变量首先会被认为是一个局部变量。如果没有找到该局部变量，则会搜索location 对象，看它是否有一个同名的属性。如果有，则该变量会被求值为location 对象的属性。
**严格模式不允许使用with 语句，否则会抛出错误。**

### 3.6.10 switch

在ECMAScript中switch语句可以用于所有数据类型，case的值不需要是常量，可以是变量或者表达式

```js
let num = 25;
switch (true) {
    case num < 0:
        console.log("Less than 0.");
        break;
    case num >= 0 && num <= 10:
        console.log("Between 0 and 10.");
        break;
    case num > 10 && num <= 20:
        console.log("Between 10 and 20.");
        break;
    default:
    	console.log("More than 20.");
}
```

**switch 语句在比较每个条件的值时会使用全等操作符，因此不会强制转换数据类型（比如，字符串"10"不等于数值10）。**

## 3.7函数

ECMAScript 中的函数不需要指定是否返回值。任何函数在任何时间都可以使用return 语句来返回函数的值，用法是后跟要返回的值。

**不指定返回值的函数实际上会返回特殊值undefined。**

严格模式对函数也有一些限制：
 **函数不能以eval 或arguments 作为名称；**
 **函数的参数不能叫eval 或arguments；**
 **两个命名参数不能拥有同一个名称。**
如果违反上述规则，则会导致语法错误，代码也不会执行。

# 4.变量、作用域与内存

## 4.1原始值与引用值

在把一个值赋给变量时，JavaScript 引擎必须确定这个值是原始值还是引用值。

### 4.1.1动态属性

只有引用值可以动态添加后面可以使用的属性

原始值不能有属性（尽管给原始值添加属性不会报错，但是访问的时候undefined）

### 4.1.2复制值

除了存储方式不同，原始值和引用值在通过变量复制时也有所不同。

在通过变量把一个原始值赋值到另一个变量时，原始值会被复制到新变量的位置。

在把引用值从一个变量赋给另一个变量时，存储在变量中的值也会被复制到新变量所在的位置。区别在于，这里复制的值实际上是一个指针，它指向存储在堆内存中的对象。操作完成后，两个变量实际上指向同一个对象。

### 4.1.3传递参数

ECMAScript 中所有函数的参数都是按值传递的。

原始值就是原始值的copy,引用类型传递的是指针

```js
function setName(obj) {
    obj.name = "Nicholas";
    obj = new Object();
    obj.name = "Greg";
}
let person = new Object();
setName(person);
console.log(person.name); // "Nicholas"
```

**typeof在检测正则时会返回function(在Safari和Chrome中)**

**IE和Firefox则是正常的Object**

## 4.2执行上下文与作用域

变量或函数的上下文决定了它们可以访问哪些数据，以及它们的行为。每个上下文都有一个关联的**变量对象**，而这个上下文中定义的所有变量和函数都存在于这个对象上。

上下文在其所有代码都执行完毕后会被销毁，包括定义在它上面的所有变量和函数（全局上下文在应用程序退出前才会被销毁，比如关闭网页或退出浏览器）。

每个函数调用都有自己的上下文。当代码执行流进入函数时，函数的上下文被推到一个上下文栈上。在函数执行完之后，上下文栈会弹出该函数上下文，将控制权返还给之前的执行上下文。ECMAScript程序的执行流就是通过这个上下文栈进行控制的。

上下文中的代码在执行的时候，会创建变量对象的一个**作用域链**。这个作用域链决定了各级上下文中的代码在访问变量和函数时的顺序。代码正在执行的上下文的变量对象始终位于作用域链的最前端。如果上下文是函数，则其**活动对象**用作变量对象。活动对象最初只有一个定义变量：arguments。（全局上下文中没有这个变量。）作用域链中的下一个变量对象来自包含上下文，再下一个对象来自再下一个包含上下文。以此类推直至全局上下文；全局上下文的变量对象始终是作用域链的最后一个变量对象。

代码执行时的标识符解析是通过**沿作用域链逐级搜索标识符名称**完成的。搜索过程始终从作用域链的最前端开始，然后逐级往后，直到找到标识符。（如果没有找到标识符，那么通常会报错。）

```js
var color = "blue";
function changeColor() {
    let anotherColor = "red";
    function swapColors() {
        let tempColor = anotherColor;
        anotherColor = color;
        color = tempColor;
        // 这里可以访问color、anotherColor 和tempColor
    }
    // 这里可以访问color 和anotherColor，但访问不到tempColor
    swapColors();
}
// 这里只能访问color
changeColor();
```

### 4.2.1作用域链增强

虽然执行上下文主要有全局上下文和函数上下文两种（eval()调用内部存在第三种上下文），但有其他方式来增强作用域链。某些语句会导致在**作用域链前端临时添加**一个上下文，这个上下文在代码执行后会被删除。通常在两种情况下会出现这个现象，即代码执行到下面任意一种情况时：
 try/catch 语句的catch 块
 with 语句

### 4.2.2变量声明

#### var

在使用var 声明变量时，变量会被自动添加到最接近的上下文。在函数中，最接近的上下文就是函数的局部上下文。在with 语句中，最接近的上下文也是函数上下文。如果变量未经声明就被初始化了，那么它就会自动被添加到全局上下文。

```js
function add(num1, num2) {
    var sum = num1 + num2;
    return sum;
}
let result = add(10, 20); // 30
console.log(sum); // 报错：sum 在这里不是有效变量

function add(num1, num2) {
    sum = num1 + num2;
    return sum;
}
let result = add(10, 20); // 30
console.log(sum); // 30
```

var 声明会被拿到函数或全局作用域的顶部，位于作用域中所有代码之前。这个现象叫作“提升”

#### let

ES6 新增的let 关键字跟var 很相似，但它的作用域是块级的，这也是JavaScript 中的新概念。块级作用域由最近的一对包含花括号{}界定。换句话说，if 块、while 块、function 块，甚至连单独的块也是let 声明变量的作用域。

```js
{let c = 'a'}
console.log(c)//VM134:1 Uncaught ReferenceError: c is not defined
```

let 与var 的另一个不同之处是在同一作用域内不能声明两次。重复的var 声明会被忽略，而重复的let 声明会抛出SyntaxError。

#### const

使用const 声明的变量必须同时初始化为某个值。一经声明，在其生命周期的任何时候都不能再重新赋予新值。

const 声明只应用到顶级原语或者对象。换句话说，赋值为对象的const 变量不能再被重新赋值为其他引用值，但对象的键则不受限制。

如果想让整个对象都不能修改，可以使用Object.freeze()，这样再给属性赋值时虽然不会报错，但会静默失败：

#### 标识符查找

当在特定上下文中为读取或写入而引用一个标识符时，必须通过搜索确定这个标识符表示什么。搜索开始于作用域链前端，以给定的名称搜索对应的标识符。如果在局部上下文中找到该标识符，则搜索停止，变量确定；如果没有找到变量名，则继续沿作用域链搜索。（注意，作用域链中的对象也有一个原型链，因此搜索可能涉及每个对象的原型链。）这个过程一直持续到搜索至全局上下文的变量对象。如果仍然没有找到标识符，则说明其未声明。

```js
var color = 'blue';
function getColor() {
	return color;
}
console.log(getColor()); // 'blue'
```

如果**局部上下文中有**一个同名的标识符，那就**不能在该上下文中引用父**上下文中的同名标识符

```js
var color = 'blue';
function getColor() {
    let color = 'red';
    return color;
}
console.log(getColor()); // 'red'
```

使用块级作用域声明并不会改变搜索流程，但可以给词法层级**添加额外的层次**：

```js
var color = 'blue';
function getColor() {
let color = 'red';
    {
        let color = 'green';
        return color;
    }
}
console.log(getColor()); // 'green'
```

在局部变量color 声明之后的任何代码都无法访问全局变量color，除非使用完全限定的写法window.color。

## 4.3垃圾回收

执行环境负责在代码执行时管理内存。

基本思路很简单：确定哪个变量不会再使用，然后释放它占用的内存。这个过程是周期性的，即垃圾回收程序每隔一定时间（或者说在代码执行过程中某个预定的收集时间）就会自动运行。

在浏览器的发展史上，用到过两种主要的标记策略：**标记清理**和**引用计数**。

### 4.3.1标记清理(最常用)

当变量进入上下文，比如在函数内部声明一个变量时，这个变量会被加上存在于上下文中的标记。而在上下文中的变量，逻辑上讲，永远不应该释放它们的内存，因为只要上下文中的代码在运行，就有可能用到它们。当变量离开上下文时，也会被加上离开上下文的标记。

垃圾回收程序运行的时候，会标记内存中存储的所有变量（记住，标记方法有很多种）。然后，它会将所有在上下文中的变量，以及被在上下文中的变量引用的变量的标记去掉。在此之后再被加上标记的变量就是待删除的了，原因是任何在上下文中的变量都访问不到它们了。随后垃圾回收程序做一次内存清理，销毁带标记的所有值并收回它们的内存。

### 4.3.2引用计数

每个值都记录它被引用的次数。声明变量并给它赋一个引用值时，这个值的引用数为1。如果同一个值又被赋给另一个变量，那么引用数加1。类似地，如果保存对该值引用的变量被其他值给覆盖了，那么引用数减1。当一个值的引用数为0 时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。垃圾回收程序下次运行的时候就会释放引用数为0 的值的内存。

严重的问题：**循环引用**。所谓循环引用，就是对象A 有一个指针指向对象B，而对象B 也引用了对象A。

```js
function problem() {
    let objectA = new Object();
    let objectB = new Object();
    objectA.someOtherObject = objectB;
    objectB.anotherObject = objectA;
}
```

一种解决办法就是设为null

### 4.3.3性能

垃圾回收程序会周期性运行，如果内存中分配了很多变量，则可能造成性能损失，因此垃圾回收的时间调度很重要。尤其是在内存有限的移动设备上，垃圾回收有可能会明显拖慢渲染的速度和帧速率。

在某些浏览器中是有可能（但不推荐）主动触发垃圾回收的。在IE 中，window.CollectGarbage()方法会立即触发垃圾回收。在Opera 7 及更高版本中，调用window.opera.collect()也会启动垃圾回收程序。

### 4.3.4内存管理

将内存占用量保持在一个较小的值可以让页面性能更好。优化内存占用的最佳手段就是保证在执行代码时只保存必要的数据。如果数据不再必要，那么把它设置为null，从而释放其引用。这也可以叫作**解除引用**。这个建议最适合全局变量和全局对象的属性。局部变量在超出作用域后会被自动解除引用

```js
function createPerson(name){
    let localPerson = new Object();
    localPerson.name = name;
    return localPerson;
}
let globalPerson = createPerson("Nicholas");
// 解除globalPerson 对值的引用
globalPerson = null;
```

解除对一个值的引用**并不会自动导致相关内存被回收**。解除引用的关键在于确保相关的值已经不在上下文里了，因此它**在下次垃圾回收时会被回收**。

##### ①通过const和let声明提升性能

在块作用域比函数作用域更早终止的情况下，就有可能更早地让垃圾回收程序介入

##### ②隐藏类和删除操作

运行期间，V8 会将创建的对象与隐藏类关联起来，以跟踪它们的属性特征。能够共享相同隐藏类的对象性能会更好，V8 会针对这种情况进行优化，但不一定总能够做到。比如

```js
function Article() {
	this.title = 'Inauguration Ceremony Features Kazoo Band';
}
let a1 = new Article();
let a2 = new Article();
```

V8 会在后台配置，让这两个类实例共享相同的隐藏类，因为这两个实例共享同一个构造函数和原型。假设之后又添加了下面这行代码：

```js
a2.author = 'Jake';
```

此时两个Article 实例就会对应两个不同的隐藏类。根据这种操作的频率和隐藏类的大小，这有可能对性能产生明显影响。

当然，解决方案就是避免JavaScript 的“先创建再补充”（ready-fire-aim）式的动态属性赋值，并在构造函数中一次性声明所有属性，如下所示：

```js
function Article(opt_author) {
    this.title = 'Inauguration Ceremony Features Kazoo Band';
    this.author = opt_author;
}
let a1 = new Article();
let a2 = new Article('Jake');
```

这样，两个实例基本上就一样了（不考虑hasOwnProperty 的返回值），因此可以共享一个隐藏类，从而带来潜在的性能提升.

但是使用delete关键字会导致生成相同的隐藏类片段

```js
function Article() {
    this.title = 'Inauguration Ceremony Features Kazoo Band';
    this.author = 'Jake';
}
let a1 = new Article();
let a2 = new Article();
delete a1.author;
```

在代码结束后，即使两个实例使用了同一个构造函数，它们也不再共享一个隐藏类。动态删除属性与动态添加属性导致的后果一样。最佳实践是**把不想要的属性设置为null**,代替delete。

##### ③内存泄漏

JavaScript 中的内存泄漏大部分是由不合理的引用导致的。

**意外声明全局变量是最常见但也最容易修复的内存泄漏问题。**

```js
function setName() {
	name = 'Jake';
}
```

window不被清理，name就不会消失

**定时器也可能会悄悄地导致内存泄漏。比如定时器的回调通过闭包引用了外部变量**：

```js
let name = 'Jake';
setInterval(() => {
	console.log(name);
}, 100);
```

定时器一直运行，name就会一直占用内存

**使用JavaScript 闭包很容易在不知不觉间造成内存泄漏。**

```js
let outer = function() {
    let name = 'Jake';
    return function() {
    	return name;
	};
};
```

只要返回的函数存在就不能清理name

##### ④静态分配与对象池

开发者无法直接控制什么时候开始收集垃圾，但可以间接控制触发垃圾回收的条件。

理论上，如果能够合理使用分配的内存，同时避免多余的垃圾回收，那就可以保住因释放内存而损失的性能。

**不要动态创建对象**

初始化的时候创建对象池，来管理一组可回收的对象，应用程序可以向对象池请求对象设置属性，操作完成后再返回给对象池。由于没发生对象初始化，垃圾回收探测就不会发现有对象更替，垃圾回收程序就不会那么频繁地运行

以下是一个对象池的伪实现：

```js
// vectorPool 是已有的对象池
let v1 = vectorPool.allocate();
let v2 = vectorPool.allocate();
let v3 = vectorPool.allocate();
v1.x = 10;
v1.y = 5;
v2.x = -3;
v2.y = -6;
addVector(v1, v2, v3);
console.log([v3.x, v3.y]); // [7, -1]
vectorPool.free(v1);
vectorPool.free(v2);
vectorPool.free(v3);
// 如果对象有属性引用了其他对象
// 则这里也需要把这些属性设置为null
v1 = null;
v2 = null;
v3 = null;
```

**静态分配(极端不考虑)**

# 5.基本引用类型



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