![](https://tva1.sinaimg.cn/large/008i3skNly1guxmhurl18j60u00u7dhh02.jpg)





[TOC]



## Webpack 是什么?

Webpack 是一种前端资源构建工具，一个静态模块打包器。

- 前端资源构建工具：主要理解一下这个前端资源是哪些资源。这些前端资源就是浏览器不认识的 web 资源， 比如 sass、less、ts，包括 js 里的高级语法。这些资源要能够在浏览器中正常工作，必须一一经过编译处理。而 webpack 就是可以集成这些编译工具的一个总的构建工具。
- 静态模块打包器：静态模块就是 web 开发过程中的各种资源文件，webpack 根据引用关系，构建一个依赖关系图，然后利用这个关系图将所有静态模块打包成一个或多个 bundle 输出。

## 为什么我们需要 Webpack

回答这个问题，可以和还没有 Webpack、没有构建工具时对比一下，就能明显地感觉出来了。这里就来列举一下不使用构建工具时的痛点。

- web 开发时调用后端接口跨域，需要其他工具代理或者其他方式规避。
- 改动代码后要手动刷新浏览器，如果做了缓存还需要清缓存刷新。
- 因为 js 和 css 的兼容性问题，很多新语法学习了却不能使用，无论是开发效率和个人成长都受影响。
- 打包问题。需要使用额外的平台如 jekins 打包，自己编写打包脚本，对各个环节如压缩图片，打包 js、打包 css 都要一一处理。 ......

而这些问题，Webpack 都提供了解决方案，你只需要做一些简单的配置就可以上手使用了。当然，Webpack 做的还不止这些，下面就来一一介绍。

## Webpack 核心配置

### entry

入口（entry）：指示 Webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图。

```
    // string方式： 单入口，打包形成一个chunk，输出一个buldle文件。chunk的名称默认是main.js
    entry: "./src/index.js",
    // array方式：多入口，所有入口文件最终只会形成一个chunk，输出出去只有一个bundle文件
    entry: ["./src/index.js", "./src/test.js"],
    // object：多入口，有几个入口文件就形成几个chunk，输出几个bundle文件。此时chunk的名称就是对象key值
    entry:{
        index:"./src/index.js",
        test:"./src/test.js",
    }
```

 

### output

输出（output）：指示 Webpack 打包后的资源 bundles 输出到哪里，以及如何命名。

```js
    output: {
        // 输出文件目录（将来所有资源输出的公共目录，包括css和静态文件等等）
        path: path.resolve(__dirname, "dist"), //默认
        // 文件名称（指定名称+目录）
        filename: "[name].js", // 默认
        // 所有资源引入公共路径前缀，一般用于生产环境，小心使用
        publicPath: "",
        /* 
        非入口文件chunk的名称。所谓非入口即import动态导入形成的chunk或者optimization中的splitChunks提取的公共chunk
        它支持和 filename 一致的内置变量
        */
        chunkFilename: "[contenthash:10].chunk.js",
        clean: true, 打包前清空输出目录，相当于clean-webpack-plugin插件的作用。
        /* 当用 Webpack 去构建一个可以被其他模块导入使用的库时需要用到library */
        library: {
            name: "[name]",//整个库向外暴露的变量名
            type: "window"//库暴露的方式
        }
    },
```

 

### loader

Loader：Webpack 自身只能理解 JavaScript 和 json 文件，loader 让 Webpack 能够处理其他文件。
 这里列举几类常见文件的loader配置。

```js
rules: [
    {
        // 匹配哪些文件
        test: /\.css$/,
        // 使用哪些loader进行处理。执行顺序，从右至左，从下至上
        use: [
            // 创建style标签，将js中的样式资源（就是css-loader转化成的字符串）拿过来，添加到页面head标签生效
            "style-loader",
            // 将css文件变成commonjs一个模块加载到js中，里面的内容是样式字符串
            "css-loader",        
             {

                 // css 兼容处理 postcss，注意需要在package.json配置browserslist
                 loader: "postcss-loader",
                 options: {
                     postcssOptions: {
                         ident: "postcss",
                         // postcss-preset-env插件：帮postcss找到package.json中的browserslist配置，根据配置加载指定的兼容性样式      
                         plugins: [require("postcss-preset-env")()],
                     },
                 },
             },
        ],
    },
    {
        test: /\.js$/,
        // 注意需要在package.json配置browserslist，否则babel-loader不生效
        // js兼容处理 babel
        loader: "babel-loader", // 规则只使用一个loader时推荐写法
        options: {
            presets: [
                [
                    "@babel/preset-env",// 预设：指示babel做怎么样的兼容处理 
                    {
                        useBuiltIns: "usage", //按需加载
                        corejs: {
                            version: "3",
                        },
                        targets: "defaults",
                    }
                ]
            ]
        }
    },
    /* 
    Webpack5.0新增资源模块(asset module)，它是一种模块类型，允许使用资源文件（字体，图标等）而无需     配置额外 loader。支持以下四个配置
    asset/resource 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现。
    asset/inline 导出一个资源的 data URI。之前通过使用 url-loader 实现。
    asset/source 导出资源的源代码。之前通过使用 raw-loader 实现。
    asset 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资     源体积限制实现。
    */
    // Webpack4使用file-loader实现
    {
        test: /\.(eot|svg|ttf|woff|)$/,
        type: "asset/resource",
        generator: {
            // 输出文件位置以及文件名
            filename: "fonts/[name][ext]"
        },
    },
    // Webpack4使用url-loader实现
    {
        //处理图片资源
        test: /\.(jpg|png|gif|)$/,
        type: "asset",
        generator: {
            // 输出文件位置以及文件名
            filename: "images/[name][ext]"
        },
        parser: {
            dataUrlCondition: {
                maxSize: 10 * 1024 //超过10kb不转base64
            }
        }
    },
],
```



### plugin

插件（plugins）：可以用于执行范围更广的任务。从打包优化和压缩，一直到重新定义环境中的变量等。

```js
// CleanWebpackPlugin帮助你在打包时自动清除dist文件，学习时使用比较方便
// const { CleanWebpackPlugin } = require("clean-webpack-plugin"); //从webpack5开始，webpack内置了该功能，只要在ouput中配置clear为true即可

// HtmlWebpackPlugin帮助你创建html文件，并自动引入打包输出的bundles文件。支持html压缩。
const HtmlWebpackPlugin = require("html-webpack-plugin");

// 该插件将CSS提取到单独的文件中。它会为每个chunk创造一个css文件。需配合loader一起使用
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

// 该插件将在Webpack构建过程中搜索CSS资源，并优化\最小化CSS
const OptimizeCssAssetsWebpackPlugin = require("optimize-css-assets-webpack-plugin");

// vue-loader V15版本以上，需要引入VueLoaderPlugin插件，它的作用是将你定义过的js、css等规则应用到vue文件中去。
const { VueLoaderPlugin } = require('vue-loader')

module.exports = {
    module: {
        rules: [
            {
                test: /\.vue$/,
                loader: "vue-loader"
            },
            {
                test: /\.css$/,
                use: [
                    // MiniCssExtractPlugin.loader的作用就是把css-loader处理好的样式资源（js文件内），单独提取出来 成为css样式文件
                    MiniCssExtractPlugin.loader,//生产环境下使用，开发环境还是推荐使用style-loader
                    "css-loader",
                ],
            },
        ],
    },
    plugins: [
        new HtmlWebpackPlugin({
            template:"index.html"
        }),
        new MiniCssExtractPlugin({
            filename: "css/built.css",
        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new VueLoaderPlugin(),
    ]
}
```

 

### mode

模式（mode）:指示 Webpack使用相应模式的配置。默认为production。
 搬一下官网的表格，还是有必要知道一下我们平常使用最多的两种模式，Webpack都做了什么。

| 选项        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为 development. 为模块和 chunk 启用有效的名。 |
| production  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为 production。为模块和 chunk 启用确定性的混淆名称，FlagDependencyUsagePlugin，FlagIncludedChunksPlugin，ModuleConcatenationPlugin，NoEmitOnErrorsPlugin 和 TerserPlugin 。 |
| none        | 不使用任何默认优化选项                                       |

- DefinePlugin：定义全局变量process.env.NODE_ENV，区分程序运行状态。
- FlagDependencyUsagePlugin：标记没有用到的依赖。
- FlagIncludedChunksPlugin：标记chunks，防止chunks多次加载。
- ModuleConcatenationPlugin：作用域提升(scope hosting)，预编译功能，提升或者预编译所有模块到一个闭包中，提升代码在浏览器中的执行速度。
- NoEmitOnErrorsPlugin：防止程序报错，就算有错误也继续编译。
- TerserPlugin：压缩js代码。

 

### 其他常用配置

```
module.exports = {
    // 解析模块的规则：
    resolve: {
        // 配置 解析模块路径别名：可简写路径。
        alias: {
            "@": path.resolve(__dirname, "src")
        },
        // 配置 省略文件路径的后缀名。默认省略js和json。也是webpack默认认识的两种文件类型
        extensions: [".js", ".json", ".css"], // 新加css文件
        // 告诉webpack解析模块是去找哪个目录
        // 该配置明确告诉webpack，直接去上一层找node_modules。
        modules: [path.resolve(__dirname, "../node_modules")],
    },
    // devServer（开发环境下配置）：
    devServer: {
        // 运行代码的目录
        contentBase: path.resolve(__dirname, "build"),
        // 为每个静态文件开启gzip压缩
        compress: true,
        host: "localhost",
        port: 5000,
        open: true, // 自动打开浏览器
        hot: true, //开启HMR功能
        // 设置代理
        proxy: {
            // 一旦devServer(5000端口)接收到/api/xxx的请求，就会用devServer起的服务把请求转发到另外一个服务器（3000）
            // 以此来解决开发中的跨域问题
            api: {
                target: "htttp://localhost:3000",
                // 发送请求时，请求路径重写：将/api/xxx  --> /xxx （去掉/api）
                pathRewrite: {
                    "^api": "",
                },
            },
        },
    },

    // optimization（生产环境下配置）
    optimization: {
        // 提取公共代码
        splitChunks: {
            chunks: "all",
        },
        minimizer: [
            // 配置生产环境的压缩方案：js和css
            new TerserWebpackPlugin({
                // 多进程打包
                parallel: true,
                terserOptions: {
                    // 启动source-map
                    sourceMap: true,
                },
            }),
        ],
    },
};
```

 

## webpack 打包优化



### 开发环境优化

##### 一、 使用source-map

source-map：一种提供源代码到构建后代码映射的技术，如果构建后代码出错了，通过映射可以追踪源代码错误。优化代码调试。
 开启source-map配置很简单：devtool:"source-map"。source-map的值有多种类型，简单解释下。
 source-map 各选项常用组成：[inline-|eval-][cheap-[module-]]source-map

- inline：内联，一个chunk生成一个总的source-map
- eval：内联，每一个文件生成一个source-map
- cheap：外部，报错位置只能精确到行。
- cheap-module：显示第三方库的source-map

内联和外部的区别： 内联不生成 map.js 文件，而是通过 data-url 的形式直接注入到 chunk 里；内联构建速度更快。

##### 二、 HMR（模块热替换）

devServer启动一个代理服务器。启动过后修改代码就会自动刷新浏览器了，但这个并不是HMR。
 HMR：模块热替换，也可以理解为局部替换。替换、添加或删除 模块，而无需重新加载整个页面。如下配置开启HMR

```js
    devServer: {
        contentBase: path.resolve(__dirname, "dist"),
        hot: true,//开启HMR功能
    },
    // 注意：Webpack升级到5.0后，target默认值值会根据package.json中的browserslist改变，导致devServer的自动更新失效。所以development环境下直接配置成web。
    target: "web",
```

开启HMR后，还需要进行一些配置才能生效。

- 样式文件：style-loader 内部实现，所以只要loader中配置了style-loade就可直接使用HMR功能
- vue文件：vue-loader内部实现，同理配置vue-loader直接使用HMR。
- js文件：需要修改源代码，接收更新通知，代码如下

```js
import test from "./test.js"
if(module.hot){
    module.hot.accept("./test.js",()=>{
        console.log('Accepting the updated test module!');
    })
}
```

当test文件被改动时，更新事件会一层层往上传递，直到传递到入口文件中。而在传递的过程中，任何地方接收了这个更新事件，即上面的module.hot.accept方法，就会停止传递，执行回调。如果一直未接收，最后就会通知Webpack刷新整个页面。

 

### 生产环境优化

##### 一、 oneOf

默认情况下，文件会去匹配rules下面的每一个规则，即使已经匹配到某个规则了也会继续向下匹配。而如果将规则放在 oneOf 属性中，则一旦匹配到某个规则后，就停止匹配了。

```js
rules:[
    {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
    },
    {
        //  以下loader一种文件只会匹配一个 
        oneOf: [
            // 不能有两个配置处理同一种类型文件，如果有，另外一个规则要放到外面。
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [
                    {
                        loader: "babel-loader",
                    },
                ],
            },
            {
                test: /\.css$/,
                use: [
                    "style-loader",
                    "css-loader",
                ],
            },
        ],
    },
]
```

放在oneOf属性中的规则只会匹配成功一次，所以如果有一种类型的文件需要使用多个loader，要么使用use数组，要么放到oneOf之外。

##### 二、 缓存

在编译打包时可对文件做缓存，有两种方式，一种是解析文件的loader自身带有缓存功能（如babel-loader,vue-loader），第二种就是使用专门的loader（cache-loader）。
 开启缓存后，对于未改动的文件，webpack直接从缓存中读取而不用再次编译，大大加快构建速度。

```js
{
    test: /\.js$/,
    use: [
        //使用cache-loader，放在babel-loader前可对babel编译后的js文件做缓存。
        "cache-loader",
        {
            loader: "babel-loader",
            options: {
                presets: [
                    [
                        "@babel/preset-env",// 预设：指示babel做怎么样的兼容处理 
                    ]
                ],
                // 开启babel缓存，第二次构建时，会读取之前的缓存。
                cacheDirectory: true,
            }
        }

}

复制代码
```

##### 三、 多进程打包（thread-loader）

一般只有在编译花费时间较长时才需要使用 thread-loader，因为这个 loader 启动和通信都是有开销的，如果时间较短，使用这个 loader 就得不偿失了。

```js
// "thread-loader"放在babel-loader前，就会在babel-loader工作时进行多进程工作。
{
    loader: "thread-loader",
    options: {
        workers: 2, // 启动进程个数，默认是电脑cpu核数-1
    },
},
{
    loader: "babel-loader",
    options: {
        presets: [
            [
                "@babel/preset-env",
            ],
        ],
    },
},
复制代码
```

##### 四、 外部扩展(externals)

externals 用来告诉 Webpack 要构建的代码中使用了哪些不用被打包的模块，这些模块可能是通过外部环境（如CDN）引入的。

```js
module.export = {
  externals: {
    // 把导入语句里的 jquery 替换成运行环境里的全局变量 jQuery
    jquery: 'jQuery'
  }
}

// 源代码
 import $ from "jquery"
复制代码
```

配置了externals后，即使你代码中引入了这个库，Webpack也不会将库打包进bundle，而是直接使用全局变量。

##### 五、 DLL

dll（动态链接库）：使用dll技术对公共库进行提前打包，可大大提升构建速度。公共库一般情况下是不会有改动的，所以这些模块只需要编译一次就可以了，并且可以提前打包好。在主程序后续构建时如果检测到该公共库已经通过dll打包了，就不再对其编译而是直接从动态链接库中获取。 实现dll打包需要以下三步：

1. 抽取公共库，打包到一个或多个动态链接库中。
2. 将打包好的动态链接库在页面中引入。
3. 主程序使用了动态链接库中的公共库时，不能被打包入bundle，应该直接去动态链接库中获取。

针对这个步骤的代码

1 新建一个webpack.dll.js用来提前打包动态链接库

```js
// webpack.dll.js
module.exports = {
    // JS 执行入口文件
    entry: {
        // 把 vue 相关模块的放到一个单独的动态链接库
        vendor: ['vue', 'axios'],
        // 其他模块放到另一个动态链接库
        other: ['jquery', 'lodash'],
    },
    output: {
        // 输出的动态链接库的文件名称，[name] 代表当前动态链接库的名称（"vendor"和"other"）
        filename: '[name].dll.js',
        // 输出的文件都放到 dist 目录下的dll文件夹中
        path: path.resolve(__dirname, 'dist', "dll"),
        // 存放动态链接库的向外暴露的变量名，例如对应 vendor 来说就是 _dll_vendor
        library: '_dll_[name]',
    },
    plugins: [
        //  打包生成一个mainfest.json文件。告诉webpack哪些库不参与后续的打包，已经通过dll事先打包好了。
        new webpack.DllPlugin({
            // 动态链接库的库名，需要和 output.library 中保持一致
            // 该字段的值也就是输出的 manifest.json 文件 中 name 字段的值
            // 例如 vendor.manifest.json 中就有 "name": "_dll_vendor"
            name: '_dll_[name]',
            // 描述动态链接库的 manifest.json 文件输出时的文件名称
            path: path.join(__dirname, 'dist', "dll", '[name].manifest.json'),
        }),
    ],
};
复制代码
```

1. 在模板页index.html中引入打包好的动态链接库

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Webpack</title>
    <script src="./dll/vendor.dll.js"></script>
    <script src="./dll/other.dll.js"></script>
</head>
<body>
    <div id="app"></div>
</body>
</html>

复制代码
```

1. 在主程序的Webpack配置中使用webpack.DllReferencePlugin插件，读取webpack.DllPlugin生成的manifest.json文件，从中获取依赖关系。

```js
// webpack.config.js
module.exports = {
    mode: "production",
    plugins: [
        new HtmlWebpackPlugin({
            template: "./index.html"
        }),
        // 告诉 Webpack 使用了哪些动态链接库
        new webpack.DllReferencePlugin({
            // manifest文件告诉webpack哪些库已经通过dll事先打包好了，后续构建直接去动态链接库里获取。
            manifest: path.resolve(__dirname, "dist", "./dll/vendor.manifest.json"),
        }),
        new webpack.DllReferencePlugin({
            manifest: path.resolve(__dirname, "dist", "./dll/other.manifest.json"),
        }),
    ],
}
复制代码
```

##### 六、 Tree Shaking(树摇)

Tree Shaking(树摇)：移除 JavaScript 上下文中的未引用代码(dead-code)。将整个应用程序想象成一棵树，绿色的树叶表示实际用到的source code（源码）和library（库），灰色的树叶则表示未被使用的代码，是枯萎的树叶。为了除去这些死去的无用的树叶，你需要摇动这棵树使其落下。这就是Tree Shaking的名称由来。

```js
// 入口文件index.js
import test from "./test.js"
console.log(test.add(2, 3));

// 测试文件test.js
const add = (x, y) => x + y
const print = (msg) => {
    console.log(msg);
}
export default { add, print }

// 最终打包输出的bundle：main.js文件
!function(){"use strict";console.log(2+3)}();
复制代码
```

从上面示例可以看出，index.js中虽然引入了test文件，但是因为test文件暴露的print方法没有被使用，所以在最终打包中被去除。
 这一点在Webpack4中还做不到，Webpack4中只会去除从未被使用的模块。带入上面的例子，如果test在index.js文件中没有被用到，才会被Tree Shaking。之所以这样，是因为Webpack4默认认为所有文件的代码都是有副作用的。如何告知Webpack你的代码是否有副作用，可通过package.json中的sideEffects字段。

```js
// 所有文件都有副作用
{
 "sideEffects": true
}
// 所有文件都没有副作用，
{
 "sideEffects": false
}
// 只有这些文件有副作用，所有其他文件都可以 Tree Shaking，但会保留这些文件
{
 "sideEffects": [
  "./src/file1.js",
  "./src/file2.js"
 ]
}
复制代码
```

比如说Webpack5.0默认设置中认为样式文件是有副作用的，所以引入样式文件虽然没有被使用（样式文件肯定是不使用的）也不会被去除，但是如果设置了sideEffects：false，就会进行Tree Shaking将代码去除。
 说了这么多，到底怎么设置Tree Shaking？其实不用特别配置，只要将mode设置为"production"，Webpack就自动启用Tree Shaking了。有两点说明下：

- 源代码必须使用 静态的 ES6 模块化语法。原因是Webpack在构建时通过静态分析，分析出代码之间的依赖关系。而动态导入如require语法只有在执行时才知道导入了哪个模块，所以无法做Tree Shaking。
- 三方库无法做Tree Shaking。原因猜测是Webpack无法保证三方库导入是否会直接对程序产生影响。

##### 七、 Code Split（代码分割）

Webpack默认会将所有依赖的文件打包输出到一个bundle.js中（单入口时），当应用程序逐渐复杂，这个bundle.js文件也会越来越大，浏览器加载的速度也会越来越慢，所以就需要使用代码分割来将不同代码单独打包成不同chunk输出。主要有两种方法

一、 通过optimization将公共代码单独打包成chunk

```js
optimization: {
    splitChunks: {
        // 选择哪些 chunk 进行优化，默认async，即只对动态导入形成的chunk进行优化。
        chunks: 'all', 
        // 提取chunk最小体积
        minSize: 20000,
        // 要提取的chunk最少被引用次数
        minChunks: 1,
        // 对要提取的chunk进行分组
        cacheGroups: {
            // 匹配node_modules中的三方库，将其打包成一个chunk
            defaultVendors: {
                test: /[\\/]node_modules[\\/]/,
                // chunk名称
                name: 'vendors',
                priority: -10,
            },
            default: {
                // 将至少被两个chunk引入的模块提取出来打包成单独chunk
                minChunks: 2,
                name: 'default',
                priority: -20,
            },
        },
    },
},
```

二、 import动态导入
 当想要根据业务拆分bundle时推荐用这种方式。import动态导入的模块Webpack会将其作为单独的chunk打包。

```js
import( /* webpackChunkName: 'test' */ './test.js').then((result) => {
    console.log(result);
}).catch(() => {
    console.log('加载失败！');
});
```

