## 1.(script)标签的加载流程

有点长，见[<script>标签元素](https://www.jianshu.com/p/028635fc2ab6) 。

## 2.浏览器渲染页面的步骤

#### ①DNS解析

DNS解析是一个递归查询的过程。

本地域名服务器 - 根域名服务器 - com顶级域名服务器

查询到的缓存在本地。

#### ②TCP连接

SSL握手

```markdown
第一步，客户端给出协议版本号、一个客户端生成的随机数（Client random），以及客户端支持的加密方法。

第二步，服务器确认双方使用的加密方法，并给出数字证书、以及一个服务器生成的随机数（Server random）。

第三步，客户端确认数字证书有效，然后生成一个新的随机数（Premaster secret），并使用数字证书中的公钥，加密这个随机数，发给服务器。

第四步，服务器使用自己的私钥，获取客户端发来的随机数（即Premaster secret）。

第五步，客户端和服务器根据约定的加密方法，使用前面的三个随机数，生成"对话密钥"（session key），用来加密接下来的整个对话过程。
```

#### ③发送HTTP请求（HTTP->(SSL/TLS)->TCP->IP）(请求行，请求报头，请求正文)

有哪些请求：GET POST HEAD(1.0), PUT PATCH DELETE OPTIONS  TRACE CONNECT(1.1) 

**GET POST的区别？**

语义不同，本质上没有区别。(TCPl链接)

区别在于不同的使用场景的规范，和不同浏览器/服务器/接口的实现方式。

（一般来说GET参数在URL，POST在Request Body中，但是实际上都可以)

浏览器请求的GET/POST 和 接口中的GET/POST。

一般来说，GET产生一个TCP数据包，POST两个。GET header data 一起发送（因为就是URL），服务器200；POST先发header,服务器100 continue，浏览器继续发送data，服务器响应200。

GET如果发送body也可以两步走，先100再200。

(两次包的好处，网络环境差的情况下，两次包方便TCP验证数据完整性)

并不是所有浏览器POST都会发送两次包，比如Firefox就发送一次。

**header 和 body 分开发送是部分浏览器或框架的请求方法，不属于 post 必然行为。**

#### ④服务器处理请求并返回HTTP报文（状态码，响应报头，响应报文）

状态码是由3位数组成，第一个数字定义了响应的类别，且有五种可能取值:

- 1xx：指示信息–表示请求已接收，继续处理。

  	100 客户端继续发送请求

  ​		 101 服务器根据客户端请求切换协议，主要用于websocket和http2升级

- 2xx：成功–表示请求已被成功接收、理解、接受。

  ​		 200（成功）：请求已成功，请求所希望的响应头或数据体将随此响应返回

  ​		 201（已创建）：请求成功并且服务器创建了新的资源

  ​		 202（已创建）：服务器已经接收请求，但尚未处理

  ​		 203（非授权信息）：服务器已成功处理请求，但返回的信息可能来自另一来源

  ​	 	204（无内容）：服务器成功处理请求，但没有返回任何内容

  ​		 205（重置内容）：服务器成功处理请求，但没有返回任何内容

  ​		 206（部分内容）：服务器成功处理了部分请求 （断点续传或者视频文件等大文件加载）

- 3xx：重定向–要完成请求必须进行更进一步的操作。

  ​		 300（多种选择）：针对请求，服务器可执行多种操作。服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择 (会缓存)

  ​		 301（永久移动）：请求的网页已永久移动到新位置。服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置（不会缓存）

  ​		 302（临时移动）：服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求

  ​		 303（查看其他位置）：请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码

  ​		 305 （使用代理）：请求者只能使用代理访问请求的网页。如果服务器返回此响应，还表示请求者应使用代理

  ​		 307 （临时重定向）：服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求

- 4xx：请求有语法错误或请求无法实现。代表了客户端看起来可能发生了错误，妨碍了服务器的处理

  ​		 400（错误请求）：服务器不理解请求的语法

  ​		 401（未授权）：请求要求身份验证。对于需要登录的网页，服务器可能返回此响应。

  ​	 	403（禁止）：服务器拒绝请求

  ​	 	404（未找到）：服务器找不到请求的网页

  ​		 405（方法禁用）：禁用请求中指定的方法

  ​		 406（不接受）：无法使用请求的内容特性响应请求的网页

  ​		 407（需要代理授权）：此状态代码与 401（未授权）类似，但指定请求者应当授权使用代理

  ​		 408（请求超时）：服务器等候请求时发生超时

- 5xx：服务器端错误–服务器未能实现合法的请求。表示服务器无法完成明显有效的请求。这类状态码代表了服务器在处理请求的过程中有错误或者异常状态发生

   500（服务器内部错误）：服务器遇到错误，无法完成请求

  ​		 501（尚未实施）：服务器不具备完成请求的功能。例如，服务器无法识别请求方法时可能会返回此代码

  ​		 502（错误网关）：服务器作为网关或代理，从上游服务器收到无效响应

  ​		 503（服务不可用）：服务器目前无法使用（由于超载或停机维护）

  ​		 504（网关超时）：服务器作为网关或代理，但是没有及时从上游服务器收到请求

  ​		 505（HTTP 版本不受支持）：服务器不支持请求中所用的 HTTP 协议版本

- 特殊：

  ​		 304： 协商缓存，告诉客户端有缓存，直接使用缓存中的数据，返回的页面只有头部信息，没有内容部分

  ​	 

#### ⑤浏览器解析渲染页面

reflow和repain

refiow:DOM节点中的各个元素都是以盒模型的形式存在，浏览器去计算其位置和大小等。

repain:绘制内容。

reflow必然导致repain

浏览器在解析过程中，如果遇到请求外部资源时，如图像,iconfont,JS等。浏览器将重复前面过程下载该资源。请求过程是异步的，并不会影响HTML文档进行加载，但是当文档加载过程中遇到JS文件，HTML文档会挂起渲染过程，不仅要等到文档中JS文件加载完毕还要等待解析执行完毕，才会继续HTML的渲染过程。原因是因为JS有可能修改DOM结构，这就意味着JS执行完成前，后续所有资源的下载是没有必要的，这就是JS阻塞后续资源下载的根本原因。

#### ⑥连接结束

## 3.URL编码

"只有字母和数字[0-9a-zA-Z]、一些特殊符号"$-_.+!*'(),"[不包括双引号]、以及某些保留字，才可以不经过编码直接用于URL。"

**网址路径的编码，用的是utf-8编码。**

**查询字符串的编码，用的是操作系统的默认编码。**?mid=13218

**GET和POST方法的编码，用的是网页的编码。**

**在Ajax调用中，IE总是采用GB2312编码（操作系统的默认编码），而Firefox总是采用utf-8编码。**

解决编码混乱的方法：使用Javascript先对URL编码，然后再向服务器提交，不要给浏览器插手的机会。因为Javascript的输出总是一致的，所以就保证了服务器得到的数据是格式统一的。

escape() 老方法

encodeURI() , encodeURIComponent(),decodeURI(),decodeURIComponent();

## 4.重定向

转发和重定向。

字面意义

## 5.跨域

#### **①为什么会出现跨域问题**

出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）

#### **②什么是跨域**

当一个请求url的**协议、域名、端口**三者之间任意一个与当前页面url不同即为跨域

#### **③非同源限制**

无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB

无法接触非同源网页的 DOM

无法向非同源地址发送 AJAX 请求

#### **④跨域解决方法**

**【1】****设置document.domain解决无法读取非同源网页的 Cookie问题**

因为浏览器是通过document.domain属性来检查两个页面是否同源，因此只要通过设置相同的document.domain，两个页面就可以共享Cookie（此方案仅限主域相同，子域不同的跨域应用场景。）www.test.com / blog.test.com

```js
// 两个页面都设置
document.domain = 'test.com';
```

【2】**跨文档通信 API：window.postMessage()**

- 页面和其打开的新窗口的数据传递
- 多窗口之间消息传递
- 页面与嵌套的iframe消息传递

```js
// 父窗口打开一个子窗口
var openWindow = window.open('http://test2.com', 'title');
// 父窗口向子窗口发消息(第一个参数代表发送的内容，第二个参数代表接收消息窗口的url)
openWindow.postMessage('Nice to meet you!', 'http://test2.com');
//监听message消息
window.addEventListener('message', function (e) {
  console.log(e.source); // e.source 发送消息的窗口
  console.log(e.origin); // e.origin 消息发向的网址
  console.log(e.data);   // e.data   发送的消息
},false);
```

**【3】****JSONP**

JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，兼容性好（兼容低版本IE），缺点是只支持get请求，不支持post请求。

核心思想：网页通过添加一个<script>元素，向服务器请求 JSON 数据，服务器收到请求后，将数据放在一个指定名字的回调函数的参数位置传回来。

```html
<!--原生实现-->
<script src="http://test.com/data?callback=dosomething"></script>
<script type="text/javascript">
	function dosomething(res){
        console.log(res.data);
    }
</script>
```

```js
<!--jQuery ajax-->
$.ajax({
    url:"http://www.test.com:8080/login",
    type:"get",
    dataType:'jsonp',
    jsonCallback:'handleCallback',
    data:{}
})
```

```vue
<!--vue-->
this.$http.jsonp('http://www.test.com:8080/login',{
	params:{},
	jsonp:'handleCallback',
}).then((res)=>{
	console.log(res);
});
```

**【4】****CORS**

CORS 是跨域资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，属于跨源 AJAX 请求的根本解决方法。

1、普通跨域请求：只需服务器端设置Access-Control-Allow-Origin

2、带cookie跨域请求：前后端都需要进行设置

**【前端设置】**根据xhr.withCredentials字段判断是否带有cookie

```js
<!--原生ajax实现-->
var xhr = new XMLHttpRequest(); // IE8/9需用window.XDomainRequest兼容
 
// 前端设置是否带cookie
xhr.withCredentials = true;
 
xhr.open('post', 'http://www.domain2.com:8080/login', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('user=admin');
 
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        alert(xhr.responseText);
    }
};
```

```ajax
<!--jQuery ajax实现-->
$.ajax({
   url: 'http://www.test.com:8080/login',
   type: 'get',
   data: {},
   xhrFields: {
       withCredentials: true    // 前端设置是否带cookie
   },
   crossDomain: true,   // 会让请求头中包含跨域的额外信息，但不会含cookie
});
```

```vue
<!--vue-resource-->
Vue.http.options.credentials = true
```

```js
<!--axios-->
axios.defaults.withCredentials = true
```

**【服务端设置】**服务器端对于CORS的支持，主要是通过设置Access-Control-Allow-Origin来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问。

```apache
Apache服务器
Header set Access-Control-Allow-Origin *
```

```php
//php后台
<?php 
header("Access-Control-Allow-Origin:*")
```

```javascript
//node.js后台
var http = require('http');
var server = http.createServer();
var qs = require('querystring');
 
server.on('request', function(req, res) {
    var postData = '';
    // 数据块接收中
    req.addListener('data', function(chunk) {
        postData += chunk;
    });
    // 数据接收完毕
    req.addListener('end', function() {
        postData = qs.parse(postData);
        // 跨域后台设置
        res.writeHead(200, {
            'Access-Control-Allow-Credentials': 'true',     // 后端允许发送Cookie
            'Access-Control-Allow-Origin': 'http://www.domain1.com',    // 允许访问的域（协议+域名+端口）
            /* 
             * 此处设置的cookie还是domain2的而非domain1，因为后端也不能跨域写cookie(nginx反向代理可以实现)，
             * 但只要domain2中写入一次cookie认证，后面的跨域接口都能从domain2中获取cookie，从而实现所有的接口都能跨域访问
             */
            'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'  // HttpOnly的作用是让js无法读取cookie
        });
        res.write(JSON.stringify(postData));
        res.end();
    });
});
 
server.listen('8080');
console.log('Server is running at port 8080...');
```

```java
/*
 * 导入包：import javax.servlet.http.HttpServletResponse;
 * 接口参数中定义：HttpServletResponse response
 */
 
// 允许跨域访问的域名：若有端口需写全（协议+域名+端口），若没有端口末尾不用加'/'
response.setHeader("Access-Control-Allow-Origin", "http://www.domain1.com"); 
 
// 允许前端带认证cookie：启用此项后，上面的域名不能为'*'，必须指定具体的域名，否则浏览器会提示
response.setHeader("Access-Control-Allow-Credentials", "true"); 
 
// 提示OPTIONS预检时，后端需要设置的两个常用自定义头
response.setHeader("Access-Control-Allow-Headers", "Content-Type,X-Requested-With");
```

**【5】服务端代理（参考researchApi)**

服务端请求不会跨域

![img](https://user-gold-cdn.xitu.io/2020/4/13/1717441c9f004d9b?imageslim)

![img](https://user-gold-cdn.xitu.io/2020/4/13/1717441cb6c9453f?imageslim)

[10种跨域解决方案](https://juejin.cn/post/6844904126246027278#heading-3)

## 6.Cookie,Session,Local Storage

### 什么是cookie

cookie是保存在客户端的纯文本文件。

cookie的作用就是用来解决   如何记录客户端的用户信息

name?登陆状态？访问记录？

Cookie以name=值 的形式存储

```js
document.cookie="username=John Doe; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/";
```



注意

```
Cookie 可以创建由超文本标记语言 (HTML) 页中的客户端脚本 （例如，通过使用 Microsoft Visual Basic 脚本版或 JScript 编写的脚本），通过使用 Microsoft Win32 互联网功能 （Win32 程序InternetSetCookie 和 InternetGetCookie），或由服务器端脚本 （例如，一个 Active Server Pages [ASP] 页面上，在 Visual Basic 脚本版本中编写的脚本或通用网关接口 [CGI] 脚本）。
```



### **cookie和session**

**cookie和session都是用来跟踪浏览器用户身份的会话方式**

cookie不设置过期事件保存在内存中，随浏览器关闭结束，设置过期事件保存在硬盘中。每次请求会带上cookie。

session保存在服务端，并且sessionid被包含在cookie中。

用户禁用cookie则需要URL重写，将sessionid拼接到地址后。

单个cookie不超过4kb,session大小无限制。

**优缺点：**

cookie大小受限，功能受限(会被禁用)，安全性低，浪费带宽，但可以管理path。

session占内存，多用户在线服务器压力大，依赖cookie，创建随意，不好维护。

#### localStorage和sessionStorage

WebStorage提供了两种API：**localStorage（本地存储）**和**sessionStorage（会话存储）**

**WebStorage两个主要目标：**

```
1.提供一种在cookie之外存储会话数据的路径
2.提供一种存储大量可以跨会话存在的数据的机制
```

localStorage的生命周期是永久的，关闭页面或浏览器之后localStorage中的数据也不会消失。

sessionStorage是在同源的窗口中始终存在的数据。只要这个浏览器窗口没有关闭，即使刷新页面或者进入同源另一个页面，数据依然存在。但是sessionStorage在关闭了浏览器窗口后就会被销毁。同时独立的打开同一个窗口同一个页面，sessionStorage也是不一样的。

localStorage和sessionStorage的存储数据大小一般都是：5MB

localStorage和sessionStorage都保存在客户端，不与服务器进行交互通信

localStorage和sessionStorage只能存储字符串类型，对于复杂的对象可以使用ECMAScript提供的JSON对象的stringify和parse来处理

localStorage：**window.localStorage**
sessionStorage：**window.sessionStorage**

#### WebStorage的优点

（1）存储空间更大：cookie为4KB，而WebStorage是5MB

（2）节省网络流量：WebStorage不会传送到服务器，存储在本地的数据可以直接获取，也不会像cookie一样每次请求都会传送到服务器，所以减少了客户端和服务端的交互，节省了网络流量

（3）对于那种只需要在用户浏览一组页面期间保存而关闭浏览器后就可以丢弃的数据，sessionStorage会非常方便

（4）快速显示：有的数据存储在WebStorage上再加上浏览器本身的缓存。获取数据时可以从本地获取会比从服务器端获取快得多，所以速度更快

（5）安全性：WebStorage不会随着HTTP header发送到服务器端，所以安全性相对于cookie来说会比较高一些，不会担心截获，但是仍然存在伪造问题

（6）WebStorage提供了一些方法，数据操作比cookie方便

```
setItem(key, value) —— 保存数据，以键值对的方式存储信息
getItem(key) —— 获取数据，将键值传入，即可获取到对应的value值
removeItem(key) —— 删除单个数据，根据键值移除对应的信息
clear() —— 删除所有的数据
key(index) —— 获取某个索引的key
```

## 7.HTTP1.0 1.1 2.0

待完成

## 8.如何利用缓存

待完成



9.setCookie, 登陆token的，sessionId的设置方式



10.safari canvas 绘制canvas区域不刷新不触发浏览器重绘的问题

见活动 - 大帝的cypher挑战-结果页评语绘制

打字机效果绘制代码片段如图所示

```typescript
 const writing = (ts: number) => {
            if (ts - updatedAt > 31) {
                updatedAt = ts
                if (alreadyDrawLength < content.length) {
                    let toDraw = ""
                    let widthTemp = 0
                    for (let i = 0; i < stepGap; i++) {
                        const offset = currentStep * stepGap
                        const element = content.charAt(offset + i)
                        const measured = ctx.measureText(drawContent + element)
                        if (measured.width >= restSpace) {
                            ctx.fillText(toDraw, drawX, drawY + offsetY)
                            restSpace = maxWidth
                            drawX = 0
                            drawY += fontSize * lineHeight
                            lines++
                            toDraw = element
                            widthTemp += measured.width
                        } else {
                            toDraw += element
                            widthTemp += measured.width
                        }
                    }
                    if (toDraw) {
                        lines++
                        //插入的bugfix代码
                        ctx.strokeStyle = "transparent"
                        ctx.strokeRect(0, 0, canvas.width, canvas.height)
                        //如上
                        ctx.fillText(toDraw, drawX, drawY + offsetY)
                        drawX += widthTemp
                        restSpace -= widthTemp
                    }
                    currentStep++
                    alreadyDrawLength += stepGap
                } else {
                    return true
                }
            }
            requestAnimationFrame(writing)
        }
        requestAnimationFrame(writing)
```

使用如上的假stroke来强制触发safari浏览器的重绘



11.js浏览器复制内容到剪贴板

```react
import { useCallback } from "react"

export default function (text: string) {
    return useCallback(() => {
        if (navigator.clipboard) {
            navigator.clipboard.writeText(text)
        } else {
            const textarea = document.createElement("textarea")
            // 隐藏此输入框
            textarea.style.position = "fixed"
            textarea.style.clip = "rect(0 0 0 0)"
            textarea.style.top = "10px"
            textarea.style.left = "10px"
            textarea.style.opacity = "0"
            textarea.style.pointerEvents = "none"

            // 赋值
            textarea.value = text
            // 防止在safari中弹出键盘造成两次屏幕高度变化
            textarea.readOnly = true
            // 选中
            document.body.appendChild(textarea)
            textarea.select()
            // 复制
            document.execCommand("copy", true)
            // 移除输入框
            document.body.removeChild(textarea)
        }
    }, [text])
}
```

textArea.select()在safari中会触发键盘的弹出，需要设置readOnly来规避



12.post请求上传文件

Request Header:

​	Content-Type:multipart/form-data; boundary=(一串code)



RequestBody:

​	(-一串Code)

```
POST https://dlog.luyulight.cn/api/parser/updateMaze
401
142 ms
POST /api/parser/updateMaze HTTP/1.1
User-Agent: PostmanRuntime/7.28.4
Accept: */*
Postman-Token: 8f7cffce-19d9-4969-90b9-29d2e26a16b8
Host: dlog.luyulight.cn
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Type: multipart/form-data; boundary=--------------------------927031754929159781597071
Content-Length: 746793
 
----------------------------927031754929159781597071
Content-Disposition: form-data; name="test_file"; filename="bg.jpg"
<bg.jpg>
----------------------------927031754929159781597071
Content-Disposition: form-data; name="test_str"
test1
----------------------------927031754929159781597071--
 
HTTP/1.1 401 Unauthorized
Server: nginx/1.18.0
Date: Wed, 31 May 2023 14:22:15 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 63
Connection: keep-alive
X-Powered-By: Express
ETag: W/"3f-jEC9sqXnkufdNGuKndga59VtyJw"
 
{"statusCode":401,"message":"未登录","error":"Unauthorized"}
```

postman用view菜单-show postman console看完整请求

apifox用生成代码-看模拟的完整请求



13.sessionStorage，在同一标签页内针对每个host单独存储 ，前进后退href都不会清除sessionStorage

特别的：关闭标签页后从历史记录重新打开，sessionStorage内容依旧存在
