---
title: 基于Webpack2构建React项目 
tags: Webpack2 React ES6
grammar_cjkRuby: true
---

Webpack2发布已经有一段时间了，官网文档也趋于完善，正好借着开发官网的契机，采用了webpack2构建，有关于Webpack2的新特性，可以参考：[Webpack2新特性][1]。

本次将使用yarn来代替npm，使用yarn  init初始化项目，同npm一样会生成一个package.Json文件，有关于yarn的安装，请擢这里：[yarn安装][2]
添加webpack和webpack-dev-server：
yarn add webpack webpack-dev-server –D

新建一个webpack.config.js，index.js和build目录，配置好入口entry和出口output：

``` stylus
module.exports = {
	    entry: "./index.js",
	    output: {
	        path: path.resolve(__dirname, "build"),
	        filename: "bundle.js",
	        publicPath: "/build/"
	    }
}
```
 打开package.js,添加scripts属性，用于启动：
 

``` stylus
  "scripts": {
	    "dev": "webpack-dev-server --hot --inline"
	  }
```
新建一个index.html文件，添加如下代码：

``` stylus
<!DOCTYPE html>
	<html>
	<head>
	    <title>webpack2</title>
	</head>
	<body>
	<div id="app"></div>
	
	<script type="text/javascript" src="build/bundle.js"></script>
	</body>
	</html>
```
添加一个id为app的div，用于之后ReactDOM render的容器，script标签加载打包后的js文件，为了能够在项目中使用React，需要添加React和React-dom这两个依赖
yarn add react react-dom react-router –S
与此同时，为了能在项目中使用ES6还需要添加如下依赖：
yarn add babel-core babel-loader babel-preset-es2015 babel-preset-react –D
然后在根目录下添加一个.babelrc文件，内容如下：

``` stylus
{
	    "presets": ["es2015", "react"]
}
```
为了让webpack知道在哪里使用，需要在webpack.config.js中添加module属性：

``` stylus
    module: {
		rules: [
			{
				test: /\.jsx?$/,
				loader: "babel-loader"
			}
		]
 }
```
css预编译使用sass，需要安装node-sass和sass-loader：
yarn add node-sass sass-loader –D
为了实现css文件的单独打包，结合code-splitting，添加extra-text-webpack-plugin插件：
yarn add extra-text-webpack-plugin –D
接下来，需要在webpack.config.js文件中添加sass相关的loader并且配合上面的插件
需要在头部引入extract-text-webpack-plugin，在module属性里添加sass文件解析规则，
Plugins属性里引入该插件，这样，scss文件就单独打包出来，最后需要在index.html文件中
使用link标签引入即可：
 <link rel="stylesheet" type="text/css" href="build/style.css">
 配置文件如下：

``` stylus
const webpack = require('webpack');
	const path = require("path");
	const ExtractTextPlugin = require('extract-text-webpack-plugin');
	
	module.exports = {
	    entry: "./index.js",
	    output: {
	        path: path.resolve(__dirname, "build"),
	        filename: "bundle.js",
	        publicPath: "/build/"
	    },
	    module: {
	        rules: [
	            {
	                test: /\.jsx?$/,
	                loader: "babel-loader"
	            },
	            {
	                test: /\.scss$/,
	                use: ExtractTextPlugin.extract({
	                    fallback: 'style-loader',
	                    use: ['css-loader', 'sass-loader']
	                })
	            }
	
	        ]
	    },
	    devServer: {
	        port: 9000,
	        historyApiFallback: true
	    },
	    devtool: "cheap-module-eval-source-map",
	    plugins: [
	        new ExtractTextPlugin('style.css')
	    ]
	}
```
同样，为了实现快速打包，需要分离第三方库，这里我们用到了webpack内置的DllPlugin和DLLReferencePlugin配合使用，首先是需要创建一个lib.config.js（名字随意），添加如下代码：

``` stylus
const webpack = require('webpack');
	
	const libs = [
	    'react',
	    'react-dom',
	    'react-router'
	];
	
	module.exports = {
	    output: {
	        path: 'public/lib',
	        filename: '[name].js',
	        library: '[name]'
	    },
	    entry: {
	        libs
	    },
	    plugins: [
	        new webpack.DllPlugin({
	            path: 'manifest.json',
	            name: '[name]',
	            context: __dirname
	        })
	    ]
	}

```
entry规定了入口部分需要被分离出来的文件，output部分确立了文件路径以及文件名称，使用DllPlugin插件生成一个manifest.json文件，运行命令：
webpack --config lib.config.js
接下来，需要在webpack.config.js中引入，在plugins属性里添加一个新的依赖插件：

``` stylus
	New webpack.DllReferencePlugin({
			context: __dirname,
			manifest: require('./manifest.json')
		})
```
 最后，需要在index.html文件里田间分离打包后的libs.js文件
<script src="/public/lib/libs.js"></script>
到此为止，基本的配置就已经完成了。

 
  [1]: https://gist.github.com/sokra/27b24881210b56bbaff7
  [2]: https://yarnpkg.com/zh-Hans/docs/install