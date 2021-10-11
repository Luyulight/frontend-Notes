## 1.CSS选择器

```css
.class  //包含类
.class1.class2 //单元素同时包含多类
#id     //id选择器
*		//所有元素
element1,element2 //所有element1,2元素
div p	//div下的所有p (包含间接子元素)
div>p	//父元素为div的p
div+p	//紧跟div的首个P()  参考li+li{...}除了第一行li之外所有添加属性
p~ul	//前面有<p>的所有<ul>
a[target=_blank]	//属性选择器
a[title~=flower]  a[title*=flower]  //包含
a[title|=en] a[title^=en]			//以en开头
a[title&=en]						//以en结尾
:active :hover :focus
:link :visited :checked :invalid :optional :target
:first-child :last-child :first-of-type :last-of-type
::after ::before
::selection
伪类很多详见https://www.w3school.com.cn/cssref/css_selectors.asp
```

## 2.CSS display

### Grid布局

[详见指南](http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)

```CSS
.container {
	display:grid/inline-grid;
    grid-template-colunms: 1fr 1fr/33% 33% 33%/repeat(n,11px)/100 auto 100;
    grid-template-rows: 1fr 1fr/33vh 33vh 33vh/repeat(n,11px);
}
//最新标准，属性前的'grid-'可以略去
//网格线可以命名
grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
grid-template-rows: [r1] 100px [r2] 100px [r3] auto [[fifth-line row-5]];
//未声明指定宽高
grid-auto-colunms: 50px;
grid-auto-rows: 50px;

//行间距和列间距
grid-gap: 20px 20px
grid-row-gap: 20px;
grid-column-gap: 20px;
grid-template-areas: 'a b c'
                   	 'd e f'
                     'g h i';
grid-template-areas: "header header header"
                     "main main sidebar"
                     "footer footer footer";//不需要使用的网格区域可以用(.)表示
[header-start] [header-end]
grid-auto-flow: row/colunm; 默认横纵排序 row dense/colunm dense 紧密填满
//单元格的 内容 对齐
justify-items: start | end | center |stretch
align-items: start | end | center | stretch
place-items:<align-items> <justify-items>
//内容区域对齐 整个container相对于父元素的对齐
//space-between:项目项目间隔相等，项目和容器边框之间无间隔（项目指一列单元格或者一行单元格）
//space-around:项目两侧间隔相等，所以项目之间的间隔是项目和容器边框间隔的2倍
//space-evenly:间隔全相等
justify-content: start | end | center | stretch | space-around | space-between 						| space-evenly;
align-content:   start | end | center | stretch | space-around | space-between 					 	| space-evenly;
place-content:<align-content> <justify-content>
//项目定位：占哪些单元格
.item1 {
    grid-colunm-start:header-start  |  1;
    grid-colunm-end:header-end  |  3;
    <=> grid-colunm: <start-line>/<end-line>
    grid-row-start:2;
    grid-row-end:4;
    <=>grid-row: <start-line>/<end-line>
    grid-colunm-start: span 2; <=> grid-colunm-end: span 2;
    grid-area: e,f;
或者 grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
}
//项目自身 （单元格内容）的对齐
.item1 {
    justify-self: start | end |center | stretch;
    align-self: start | end | center | stretch;
    place-self: <align-self><justify-self>;
}
```

### Flex布局

[详见指南](https://zhuanlan.zhihu.com/p/25984121)

```css
flex-container: main axis, cross axis, main/cross方向可指定
flex items: main size/cross size 根据指定的main轴的方向来区别宽高
!!!!!!容器设置 flex 布局之后，子元素的 float、clear、vertical-align 的属性将会失效。
.container {
	display: flex | inline-flex;
	flex-direction:row | row-reverse | colunm | colunm-reverse;//主轴方向
	flex-wrap: nowrap | wrap | wrap-reverse; //容器内是否可换行
	//no-wrap不换行，如果主轴尺寸固定 溢出时 项目尺寸会被挤压
	flex-flow: <flex-direction> || <flex-wrap>;
}
//主轴方向对齐
.container {
	justify-content: flex-start | flex-end | center | space-betweem          |                      space-around;
	//flex-start/end 左右对齐(呸，起点终点对齐)
	//center居中
	//space-between两端对齐，中间间隔相等
	//space-around 每个项目两侧间隔相等，即距两边一个单位，项目项目之间两个单位
}
//交叉轴方向对齐
.container {
	align-items: flex-start | flex-end | center | baseline | stretch;
	//项目未设置height或者height:auto，默认为stretch即撑满整个容器高度
	//flex-start/end 交叉轴起终点对齐
	//center 居中
	//baseline 项目第一行文字基线对齐
}
//align-content: 定义了多根轴线的对齐方式(nowarp情况)，如果只有一根轴线，那么该属性将不起作用
.container {
    align-content: flex-start | flex-end | center | space-between | 
        			space-around | stretch;
    //多个轴线stretch会平分垂直空间
    //其他的类似于justify-content的规则
}
//元素属性
.item {
    order:<integer>;//排序的顺序
}
.item {
    flex-basis: <length> | auto; //默认占据的主轴空间，auto则是原有的width或者height
    (主要用于计算主轴剩余空间以对对齐属性分配)
    flex-grow:<number> //在存在剩余空间的情况下项目放大比例 默认值0 存在剩余空间也不放大
    flex-shrink:<number> //空间不足的情况下缩小比例 默认值1  设为0时不缩小
}
.item {
    flex: none | [<'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];
    //flex-basis,flex-grow,flex-shrink的组合
    //默认值 0 1 auto
    //快捷值 auto(1 1 auto) none(0 0 auto)
    //当 flex 取值为一个非负数字，则该数字为 flex-grow 值，flex-shrink 取 1，flex-basis 
    //当 flex 取值为 0 时，对应的三个值分别为 0 1 0%
	//当 flex 取值为一个长度或百分比，则视为 flex-basis 值，flex-grow 取 1，flex-shrink 	  取 1
    //当 flex 取值为两个非负数字，则分别视为 flex-grow 和 flex-shrink 的值，flex-basis 		 取 0%
    //当 flex 取值为一个非负数字和一个长度或百分比，则分别视为 flex-grow 和 flex-basis 的		值，flex-shrink 取 1
}
总结：！！！！！ 简写形式 flex-grow 和 flex-shrink默认都是1， flex-basis为0%
    根据输入的数字或者长度 以及输入的个数来进行部分填充修改
补充： flex-wrap设置为wrap|wrap-reserve时, flex grow起作用，flex-shrink不起作用(空间足够)
	  flex-wrap为nowrap时，都起作用
.item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
    //规则类似container的align-items，但是仅对单元素生效
}
```

**flex布局需要一些浏览器前缀以实现对大多数浏览器的兼容。**

**并且flex布局的标准一直在变。下面是一个Sass处理的一个示例：**

```scss
@mixin flexbox() {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}

@mixin flex($values) {
  -webkit-box-flex: $values;
  -moz-box-flex:  $values;
  -webkit-flex:  $values;
  -ms-flex:  $values;
  flex:  $values;
}

@mixin order($val) {
  -webkit-box-ordinal-group: $val;  
  -moz-box-ordinal-group: $val;     
  -ms-flex-order: $val;     
  -webkit-order: $val;  
  order: $val;
}

.wrapper {
  @include flexbox();
}

.item {
  @include flex(1 200px);
  @include order(2);
}
```

### Table布局

```css
简单来说就是:
table    { display: table }
tr       { display: table-row }
thead    { display: table-header-group }
tbody    { display: table-row-group }
tfoot    { display: table-footer-group }
col      { display: table-column }
colgroup { display: table-column-group }
td, th   { display: table-cell }
caption  { display: table-caption }
```

## 3.CSS position

[详见指南](http://www.ruanyifeng.com/blog/2019/11/css-position.html)

### position:static

```css
// static是position属性的默认值
//浏览器按照源码顺序决定每个元素的位置(正常的页面流 normal flow)
//每个块级元素占据自己的区块(block)，元素元素之间不重叠
!!!static定位时，元素的top,bottom,left,right属性无效
```

### position:relative

```css
//relative表示，相对(static定位)的默认位置进行偏移
//需要搭配top,bottom,left,right使用
不脱离文档流
```

### position:absolute

```css
//absolute表示,相对于上级元素(一般是父元素)进行偏移，定位基点是父元素。
//父元素不能是static定位，否则定位基点将变成整个网页的根元素。
//需要搭配top,bottom,left,right使用
脱离文档流
```

### position:fixed

```css
//fixed表示相对于 视窗 进行偏移 定位基点是浏览器窗口
//如果搭配top,bottom,left,right则计算初始位置
//否则初始位置是元素的默认位置
脱离文档流
```

### position:sticky（2017年以后）

```css
//sticky表示基于用户的滚动位置来定位。
//粘性定位的元素是依赖于用户的滚动，在relative 与 fixed 定位之间切换。
//跨越特定阈值前为相对定位，之后为固定定位。
//这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。
不脱离文档流
```

### position:sticky的一些特殊应用

```css
1.元素滚动堆叠
2.简单表格的表头固定
！！！！sticky必须设在<th>元素上面，不能设在<thead>和<tr>元素，因为这两个元素没有relative定位，也就无法产生sticky效果。
```

脱离文档流会造成父元素高度坍塌

**脱离文档流，也就是将元素从普通的布局排版中拿走，其他盒子在定位的时候，会当做脱离文档流的元素不存在而进行定位。需要注意的是，使用float脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在周围。而对于使用absolute  positioning脱离文档流的元素，其他盒子与其他盒子内的文本都会无视它。**

**文档流** 实际上是一种民间说法，官方说法是 **常规流**（normal flow）

**文档流** 的官方概念：**元素按照其在HTML文件中的位置顺序，决定它在显示器上如何排布的过程。主要的形式是自上而下，一行接一行，每一行从左至右。**

## 4.行内元素与块级元素

**行内元素：**与其他元素在一行   宽高不可设   水平方向margin,padding有效，竖直方向的没用

**块级元素：**独占一行   可设宽高，默认100%    margin，padding均有效

```html
常见行内元素：
<a>,<b>,<em>强调,<strong>粗体强调,<strike>中划线,<i>,<input>,<label>,<span>,<select>,<textarea>
!!<br>!!,<img>,
<u>下划线,<sub>下标,<sup>上标,
```

```html
常见块级(内联)元素：
<div>,<dir>,<p>,<h1>....标题,<form>,<ul>,<ol>,<dl>,<hr>水平分割线,
<menu>,<fieldset>form控制组,<pre>格式化文本,<table>
```

```css
行内元素和块级元素转换:
display:
    display:block;
    display:inline;
float:
	设置float以后，该行内元素的display会被赋予block属性
position:
	position:absolute和position:fixed会将原先的行内元素变为块级元素
```

```
inline-block的特殊性:
可以让元素既能设置宽高，又能以inline的方式默认显示。
只有inline-block的元素可以设置vertical-align属性。
但是inline-block有一个隐藏问题，其实不算问题：
设置liline-block的元素间会有间隙，
这个空隙是因为块级元素会换行，就有换行，HTML 中的换行符、空格符、制表符等合并为空白符.所以换行符会占据宽度,产生间隙 
处理的方式也有挺多，举例：
标签换行写法改为标签内回车，设置margin等等
！！特殊的：如果是底部出现间隙，设置vertical-align是top/bottom/middle都可以解决此问题。
```

参考张鑫旭的博客[去除inline-block元素间间距的N种方法](https://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%e5%8e%bb%e9%99%a4%e9%97%b4%e8%b7%9d/)

```html
可变元素：根据上下文语境决定该元素为块元素或者块级（内联）元素
<button> <del> <iframe> <object> <map> <script>
```

## 5.盒子模型

![img](https://upload-images.jianshu.io/upload_images/4160415-f06e2ce3b5971909.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

### content-box和border-box

width和height的区别，borderbox的宽高 = content的宽高+（内边距+边框宽度）*2

​										contentbox的宽高 = content的宽高

chrome默认是content-box，IE默认是border-box

如果不设置背景属性，背景默认会覆盖到border

```css
background-clip:border-box | content-box;
```

### 盒子水平居中

可以让一个盒子实现水平居中，需要满足一下两个条件：

​           块级元素和盒子必须指定宽度（width）

　　   左右的外边距都设置为auto，就可使块级元素水平居中。

### 外边距合并

margin属性有一个特别的行为，就是外边距合并，这个行为只对普通文档流中的块级元素的**垂直外边距有效；**行内框（inline-block）、浮动元素(float)和绝对定位(position:absolute)的原素不会发生外边距合并。

外边距合并主要有两种情况：

​		 1、**同一层相邻元素之间**两个或多个垂直毗邻的兄弟元素，上面元素的下边距会与下面元素的上边距发生合并，合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。

​		2、**没有内容将父元素和后代元素分开**父元素和子元素之间，父元素的上外边距和第一个子元素的上外边距、父元素的下外边距和最后一个子元素的下外边距。发生这种情况的前提是父元素和第一个（或最后一个）子元素之间不存在边框和内边距把外边距分隔开，合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。（负数正数则相加）（注意合并后都会以父元素的外边距形式展示）

​		3、**空的块级元素** 元素本身没有高度但是有上下边距，上下边距会合并。

## 6.【实践】水平垂直居中的对齐方式(10种)

### 居中元素定宽高：

#### ①absolute + 负margin 

```css
.box {
    width:200px;
    height:200px;
    padding:10px;
	position:absolute;
	top:50%;
    left:50%;
    //这里根据盒子模型需要判断的位移的距离
    margin-left:-100px | -110px;
    margin-top:-100px | -110px;
    box-sizing: border-box | content-box;
}
```

#### ②absolute + margin auto

```css
.box {
    position: absolute;;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
	width:200px;
    height:200px;
    padding:10px;
}
```

#### ③absolute + calc

```css
.box {
    position: absolute;;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
```

### 居中元素不定宽高:

#### ④absolute + transform

```css
.box {
	position: absolute;;
    top:50%;
    left:50%;
    transform: translate(-50%, -50%);
}
```

#### ⑤lineheight

```css
.container {
    width:auto;
    height:400px;
    line-height:400px;//
    text-align:center//
}
.box {
    display: inline-block;//
    vertical-align: middle;//
    line-height: initial;
}
```

vertical-align:middle有时候对单容器元素不起作用，有如下两种解决方法：

1）给container元素设置display:table-cell;和vertical-align:middle;（不推荐）

```css
.container {
    width:auto;
    height:400px;
    line-height:400px;//
    text-align:center//
    display:table-cell;
    vertical-align:middle;
}
.box {
    display: inline-block;//
    line-height: initial;
}
```

2)在box div的前或后添加一个help div,宽度为0，高度100%,设置相同

```css
.container {
    width:auto;
    height:400px;
    line-height:400px;//
    text-align:center//
}
.box {
    display: inline-block;//
    vertical-align: middle;//
    line-height: initial;
}
.help {
    width: 0;
    height: 100%;
    vertical-align: middle;
    display: inline-block;
}
```

#### ⑥writing-mode

writing-mode可以改变文字的显示方式,同时水平方向的对齐也会对应到垂直方向

但是元素不能写到.box里面了，需要再套一个内层的div

```css
 <div class="container">
    <div class="box"><div class="inner">文本文本文本</div></div>
 </div>
.container {
	width:300px;
	height:300px;
	writing-mode:vertical-lr;
	text-align:center;
}
.box {
	writing-mode:horizontal-tb;
    text-align:center;
    display: inline-block;
    width:100%;
}
.inner {
	display:inline-block;
	margin:auto;
	text-align:left;//修正文字对齐
}
```

#### ⑦table(不推荐，代码冗余)

td本身是垂直居中的，text-align:center并且内存display:inline-block即可

#### ⑧CSS-table

```css
.container {
    display:table-cell;
    text-align:center;
    vertical-align:middle;
}
.box {
    display:inline-block;
}
```

#### ⑨flex布局

```css
.container {
    display:flex;
    justify-content:center;
    align-items:center;
}
```

#### ⑩grid布局

```css
.container {
    display:grid;
    justify-content:center;/justify-items:center
    align-items:center;align-content:center
}
```

或者容器不设置，元素设置align-self;justify-self;

## 7.页面隐藏元素的办法

1.visibility:hidden; 元素占用空间，元素不影响css计数器: 只触发repaint

2.display:none; 元素不占用文档空间 ，影响css计数器（点击事件是保留的，如果有label for仍可以触发）；transition属性不支持display,不识别display:none；display:none会触发reflow;

reflow必然触发repaint

3.opacity:0; 透明度为零，仍可触发点击事件

4.transform: scale(0) 缩放位无限小，原位置保留

5.div hidden="hidden"

6.height:0,然后消除边框

7.filter:blur(0) 模糊度0

8.position:absolute 负值定位到页面外面





8.background-size:cover,contain,100%,auto的区别

https://www.w3school.com.cn/tiy/c.asp?f=css_background-size&p=7





9.Transform的 scale属性不能作用于 inline元素上，例如span

并且动画 animation  也不能作用于inline元素上

可以给span加display:inline-block;属性