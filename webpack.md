# 1.

webpack 4中css中url形式加载图片，需要配置url-loader /file-loader插件

但是遇到了打包后，在html显示url([ObjectModule])的情况



需要在url-loader/file-loader 的配置中的 options 添加 {esModule:false}才可以解决



原因是安装的css-loader版本是1.0.0过低，但是url-loader是个正常版本

在css-loader3.x某个版本下（最低3.2.0，支持了esModule）解决

原因是在于，css-loader版本低（CommonJS方式导出），而url-loader版本高（ESModule形式引入），就会造成一个不兼容的情况。

会导致如下的源码差异

url-loader不关esModule

```js
/***/ "./src/123.png":
/*!*********************!*\
  !*** ./src/123.png ***!
  \*********************/
/*! exports provided: default */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
eval("__webpack_require__.r(__webpack_exports__);\n/* harmony default export */ __webpack_exports__[\"default\"] = (__webpack_require__.p + \"0424113d78b11aa5821fbc9fdb1468ec.png\");\n\n//# sourceURL=webpack:///./src/123.png?");

/***/ }),
```

url-loader关闭esModule

```js
/***/ "./src/123.png":
/*!*********************!*\
  !*** ./src/123.png ***!
  \*********************/
/*! no static exports found */
/***/ (function(module, exports, __webpack_require__) {

eval("module.exports = __webpack_require__.p + \"0424113d78b11aa5821fbc9fdb1468ec.png\";\n\n//# sourceURL=webpack:///./src/123.png?");

/***/ }),
```

因为cssloader的版本低，所以默认以commonJS方式引入图片，

在url-loader不关闭esModule的情况下直接import.拿到的是一个ObjectModule，这个ObjectModule的default属性才是图片。

在url-loader关闭esMoudle的情况下import即可成功

# 2.

在配置file-loader对图片进行打包时，因为字体文件也需要用file-loader打包，所以写出了如下的配置

```js
{
    test: /\.(png|svg|jpg|gif)$/,
    loader: 'file-loader',
    options: {
      name: 'media/[hash:10].[ext]',
      esModule: false,
    }
},
{
    exclude: /\.(css|js|html|ts|tsx|jsx|less|scss)$/,
    loader: 'file-loader',
    options: {
      name: 'media/[hash:10].[ext]',
    }
}
```

这样做会导致实际我们在页面里使用到图片的地方，比如一个logo.svg

实际上生成后的文件会变成两个svg,一个是压缩后的logo.svg,

另一个的内容其实会变成

```svg
export default __webpack_public_path__ + "media/123d0f32b9.svg";
```

而且引入到打包后的js里面的svg则是后者

这样就会导致所有图片都没有正确加载。

原因就是误用了exclude的写法

如上配置中，exclude项因为没有test属性，所以所有的图片在走了第一个图片的test之后，还是会再进一次exclude的配置，所以说相当于一个文件走了两次file-loader。

原本的正常的svg在经过test/file-loader处理过后，生成的是一个"export......"的字符串以插入到打包好的bundle.js的对应位置，但是因为第二次走了file-loader，所以实际上又被处理成了一个指向文件的文件。就导致了如上的一个svg但是内容是

```
export default __webpack_public_path__ + "media/123d0f32b9.svg";
```

这样的一个错误结果。



解决办法其实就是，两个配置任意删除一个即可，删除下配置的话则需要在上配置的test里加入字体等文件的格式。

```js
 {
    test: /\.(png|jpg|gif)$/,
    loader: 'file-loader',
    options: {
      name: 'media/images/[hash:10].[ext]',
      esModule: false,
    }
  },
  {
    exclude: /\.(css|js|html|ts|tsx|jsx|less|scss|png|jpg|gif)$/,
    loader: 'file-loader',
    options: {
      name: 'media/[hash:10].[ext]',
    }
  }
```



而exclude的一般写法其实是用于

比如我们对于css的引用，

有的是文件的形式

```ts
import './App.css';
```

有的是模块的形式

```ts
import styles from './App.css';
```

而我们想通过处理使得这两种引用方式的css用不同的loader来处理

就可以首先通过命名的方式，对模块引入的css以 name.modules.css的形式命名，

普通的css则就是name.css

而在webpack的配置中就可以做区别处理了

```js
{
    test: /\.module\.css$/,
    use: getCssLoaders(1)
},
{
    test: /\.css$/,
    exclude: /\.module\.css$/,
    use: getCssLoaders(2)
},
```

3.在parcel项目中打包react

首先parcel入口设置为.pug文件，在.pug文件中写入html结构并且引入.ts/.tsx的react入口文件来结束打包流程。

在parcel的情况下会有className挂不上去的问题，原因是在parcel1中没有postcss的原生支持，需要安装^3.0.0版本的postcss 来完成css modules的引入

```


//TODO  LIST 
首页布局切图校准

HOME页全局 loading
页面H5响应式布局

HOME-CANCEL切换过渡动画 

按纽加入loading

协议加padding

验证页  需要一个tips


通用页脚






