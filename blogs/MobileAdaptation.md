# Vue 项目中的移动端适配方案

## 1、淘宝 flexible 方案

**工具 & 插件**
> * sublime
* cssrem -- 一个CSS值转REM的Sublime Text插件
* lib-flexible -- 安装依赖

**配置**
* 安装 lib-flexible
* 在模版文件 index.html 中设置 meta 属性

```
<meta name="viewport" content="width=device-width,inital-scale=1.0,maxmum-scale=1.0,minmum-scale=1.0,user-scalable=no">
```
* 在入口文件 index.js 中引入 lib-flexible

```
import 'lib-flexible'
```
*引入 lib-flexible 更简便的方法是直接使用阿里CDN，在模板文件 index.html 中通过script标签引入*

```
<script src="http://g.tbcdn.cn/mtb/lib-flexible/0.3.4/??flexible_css.js,flexible.js"></script>
```
> 但是这样做会有一个问题，协议是 http，如果在 https 中这样引入会出失败

+ 安装 sublime 插件
  > - [下载项目](https://github.com/flashlizi/cssrem)
  > - 复制下载好的文件至 packages 目录下
  > - 重启即可

+ 修改配置文件
进入：Sublime Text -> Preferences -> Package Settings -> cssrem

```
{
  // px 转 rem的单位比例，即 1rem = 37.52px，可以根据设计稿修改该值
  "px_to_rem": 37.52,
  // 设置 px 转化为 rem 保留几位小数
  "max_rem_fraction_length": 2,
  // 设置对哪些文件启用该插件
  "available_file_types": [".css", ".less", ".sass", ".scss", ".vue"]
}
```

## 2、lib-flexible 的另一种实现
> 前面的方案有一定局限性，比如只有在 sublime 中才能使用，而且需要手动转 px 为 rem，我们可以通过其他项目依赖实现相同的效果。

**插件**

* lib-flexible
* px2rem-loader

**使用**

* 使用 vue init webpack my-app 创建一个项目

* 安装 flexible，引入（同上）

* npm install px2rem-loader --save-dev

+ 修改配置文件
  - build -> utils.js
  
```
exports.cssLoaders = function (options) {
   const cssLoader = {
    loader: 'css-loader',
    options: {
      sourceMap: options.sourceMap
    }
  }
  
  // 引入 px2rem-loader
  const px2remLoader = {
    loader: 'px2rem-loader,
    options: {
      // px 转 rem 的比例，视设计图而定
      remUnit: 37.5
    }
  }
  
  // 添加 px2rem-loader
  function generateLoaders (loader, loaderOptions) {
    // 在下面的数组中添加 px2rem
    const loaders = options.usePostCSS ? [cssLoader, px2remLoader, postcssLoader] : [cssLoader, px2remLoader]
  }
}
```

这样的话这个 loader 会自动将我们写的 px 转为 rem，配合跟元素大小的随视口大小改变，可以实现移动端的适配。

## 3、vw / vh
>如果不考虑适配比较老旧版本的浏览器，完全可以使用 vw / vh 布局，分别表示视口宽度/高度。100vw 等于整个视口的宽度，100vh 等于整个视口的高度。

* vw : 1vw 为视口宽度的 1%
* vh : 1vh 为视口高度的 1%
* vmin : vw 和 vh 中的较小值
* vmax : 选取 vw 和 vh 中的较大值

总而言之，如果设计稿为 750px，那么 1vw = 7.5px，100vw = 750px。其实设计稿按照设么都没多大关系，最终转化过来的都是相对单位，我们可以使用插件帮助我们把书写的 px 转为 vw / vh。

不过，使用 vw 具有一定的缺陷：

>* px转换成vw不一定能完全整除，因此有一定的像素差。
* 比如当容器使用vw，margin采用px时，很容易造成整体宽度超过100vw，从而影响布局效果。当然我们也是可以避免的，例如使用padding代替margin，结合calc()函数使用等等...

**插件**

* postcss-px-to-viewport

**使用**

* npm install postcss-px-to-viewport --save-dev
* 在 postcss.config.js 添加配置
>相关配置项参考[官方 github 仓库](https://github.com/evrone/postcss-px-to-viewport/blob/master/README_CN.md)

```
module.exports = {
  "plugins": {
    "postcss-px-to-viewport": {
      options: {
        // 常用配置，更多配置参考官方
        // 需要转换的单位
        unitToConvert: 'px',
        // 设计稿的视口宽度
        viewportWidth: 375,
        // 单位转换后保留的精度
        unitPrecision: 5,
      }
    }
  }
}
```