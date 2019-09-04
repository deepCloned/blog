# 部署静态项目至 github

## 使用 vue init webpack my-app 创建的项目
* 打包设置 - 修改静态资源路径
build -> util.js

```
// Extract CSS when that option is specified
// (which is the case during production build)
if (options.extract) {
  return ExtractTextPlugin.extract({
    use: loaders,
    fallback: 'vue-style-loader',
    // 增加下面这行代码
    publicPath: '../../'
  })
} else {
  return ['vue-style-loader'].concat(loaders)
}
```

config -> index.js

```
module.exports = {
  dev: {},
  build: {
    assetsPublicPath: '/' => assetsPublicPath: './'
  }
}
```

* .gitignore 去掉忽略 dist 目录的上传，当然也可以创建一个新的分支专门用来上传和部署静态页面。

## 使用 Vue-cli 创建的项目
[官方指南](https://cli.vuejs.org/zh/guide/deployment.html#github-pages)