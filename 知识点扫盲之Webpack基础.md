# 【原创】知识点扫盲之[Webpack](https://webpack.js.org/)基础

webpack官网地址：https://webpack.js.org/ **（建议）** \
webpack中文地址：https://www.webpackjs.com/ 

**为什么需要构建工具？**
- 转换ES6语法
- 转换JSX
- CSS前缀补全/预处理器
- 压缩混淆
- 图片压缩
- ... ... 


## 1.核心知识点-entry

**1.1概念** 

Entry用于指定webpack的打包入口，要知道webpack是模块打包器，把一切的资源（css/js/图片/文本/...），全部认为是模块，模块与模块之间存在依赖关系，基于依赖关系形成了依赖树。\
当指定Entry后，webpack以此为打包入口进行打包。

**1.2用法** 

**单入口** entry是一个字符串

```TypeScript
moudle.exports = {
    entry:'./path/to/my/entry/file.js'
}
```
******

**多入口** entry是一个对象

```TypeScript
module.exports = {
    entry:{
        app: './src/app.js',
        app2: './src/app2.js'
    }
}
```


