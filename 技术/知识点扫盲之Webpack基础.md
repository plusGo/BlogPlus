# 【原创】知识点扫盲之[Webpack](https://webpack.js.org/)基础

webpack官网地址：https://webpack.js.org/ **（建议）** \
webpack中文地址：https://www.webpackjs.com/ 

![webpack](/assets/images/webpack.png "webpack")

**为什么需要构建工具？**
- 转换ES6语法
- 转换JSX
- CSS前缀补全/预处理器
- 压缩混淆
- 图片压缩
- ... ... 


## 1.核心知识点-entry

**1.1 概念** 

Entry用于指定webpack的打包入口，要知道webpack是模块打包器，把一切的资源（css/js/图片/文本/...），全部认为是模块，模块与模块之间存在依赖关系，基于依赖关系形成了依赖树。\
当指定Entry后，webpack以此为打包入口进行打包。

**1.2 用法** 

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

## 2.核心知识点-Output

**2.1 概念**

Output用于告诉webpack如何将编译后的文件输出到磁盘

**单入口配置**
```TypeScript
moudle.exports = {
    entry:'./path/to/my/entry/file.js',
    output:{
        filename:'bundle.js',
        path: __dirname + '/dist'
    }   
}
```

**多入口配置**
```TypeScript
moudle.exports = {
    entry:{
        app: './src/app.js',
        app2: './src/app2.js'
    },
    output:{
        filename:'[name].js',
        path: path.join(__dirname,'dist')
    }   
}
```

## 3.核心知识点-Loaders

**3.1 概念**

webpack开箱即用只支持JS和JSON两种文件类型，通过Loaders支持其他文件类型并且把他们转化成有效的模块，并且可以添加到依赖图中。

本身是一个函数，接受源文件作为参数，返回转换的结果。

**3.2 常见Loaders**

| 名称 | 描述 |
| ----- | ----- |
| babel-loader | 转换ES6、ES7等JS新特性语法 |
| css-loader | 支持.css文件的加载和解析 |
| style-loader | 将样式注入到head标签中 |
| less-loader | 将less文件转换成css |
| ts-loader | 将TS转换成JS |
| file-loader | 将图片、字体等打包 |
| raw-loader | 将文件以字符串的形式导入 | 
| thread-loader | 多进程打包JS和CSS |

**3.3 Loaders的用法**

```typescript
const path = require('path')

module.exports = {
    output:{
        filename:'bundle.js'
    },
    module:{
        rules:[
            {test:/\.txt$/,use:'raw-loader'} // test 指定匹配规则 use指定使用的loader名称 
        ]   
    }   
}
```

## 4.核心概念-Plugins

**4.1 概念**

插件用于bundle文件的优化、资源管理和环境变量的注入，并且作用于整个构建过程

**4.2 常见Plugins**

| 名称 | 描述 |
| ----- | ----- |
| CommonsChunkPlugin | 将chunks相同的模块提取成公共的js |
| CleanWebpackPlugin | 清理构建目录 |
| ExtractTextWebpackPlugin | 将CSS从bundle文件里提取成一个独立的CSS文件 |
| CopyWebpackPlugin | 将文件或者文件夹拷贝构建的输出目录 |
| HtmlWebpackPlugin | 创建html文件去承载输出的bundle |
| [MiniCssExtractPlugin](https://github.com/webpack-contrib/mini-css-extract-plugin) | 压缩CSS文件 |
| [TerserWebpackPlugin](https://github.com/webpack-contrib/terser-webpack-plugin) | 压缩混淆JS |
| ZipWebpackPlugin | 将打包的资源生成一个zip包 |

**4.3 Plugins的用法**

```typescript
const path = require('path')

module.exports = {
    output:{
        filename:'bundle.js'
    },
    plugins:[
        new HtmlWebpackPlugin({template:'./src/index.html'})
    ]   
}
```
## 5.核心概念-Mode

**5.1 概念**

Mode用来指定当前的构建环境是：production、development还是none

设置mode可以使用webpack的内置函数，默认值为production

**5.2 内置函数功能**

| 选项 | 描述 |
| ----- | ----- |
| development | 设置process.env.NODE_ENV的值为development.开启NamesChunksPlugin和NamedModulesPlugin |
| production | 设置process.env.NODE_ENV的值为production.开启FlagDependencyUsagePlugin、FlagIncludedChunkPlugin、ModuleConcatenationPlugin、NoEmitOnErrorPlugin、OccurrenceOrderPlugin、SideEffectsFlagPlugin和TerserPlugin |
| none | 不开启任何优化项目 |
