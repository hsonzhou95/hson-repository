---
title: webpack+vue-cil框架的搭建
date: 2019-12-28 14:42:56
tags:
- 响应式
- 样式
comments: true
type: "webpack"
categories:
- webpack
---
## Webpack
### 前言

##### 现如今的开发当中，webpack的使用越来越频繁，而我们也经常需要使用诸如vue/cli create-react-app等脚手架来搭建我们的项目，所以对webpack的学习必不可少，下面我们一起进入到webpack的学习当中。
<!-- more -->
### 1 入门(一起来用这些小例子让你熟悉webpack的配置)

![avtvor]()

### 1.1初始化项目

###### 新建一个目录，初始化npm

```
npm init 
```

##### webpack是运行在node环境中的，我们需要安装以下两个npm包

```
npm i -D webpack webpack-cli 
```

- npm i -D 为npm install --save-dev的缩写
- npm i -S 为npm install --save的缩写

##### 新建一个文件夹`src` ,然后新建一个文件`main.js`,写一点代码测试一下

```
console.log('calle me ')
```

##### 配置package.json命令

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/w1.png?token=AMCTPYZ4L67I6Q2MRG6XTBK6AXEAM)

##### 执行

```
npm run build
```

##### 此时如果生成了一个dist文件夹，并且内部含有main.js说明已经打包成功了

### 1.2开始我们自己的配置

##### 上面一个简单的例子只是webpack自己默认的配置，下面我们要实现更加丰富的自定义配置，新建一个`build`文件夹,里面新建一个`webpack.config.js`

```javascript
// webpack.config.js

const path = require('path');
module.exports = {
    mode:'development', // 开发模式
    entry: path.resolve(__dirname,'../src/main.js'),    // 入口文件
    output: {
        filename: 'output.js',      // 打包后的文件名称
        path: path.resolve(__dirname,'../dist')  // 打包后的目录
    }
}

```

##### 更改我们的打包命令

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p2.png?token=AMCTPYZG7LPE7YFZCHEIW7K6AXELQ)

```
执行 npm run build 会发现生成了以下目录（图片）
其中dist文件夹中的main.js就是我们需要在浏览器中实际运行的文件
当然实际运用中不会仅仅如此,下面让我们通过实际案例带你快速入手webpack
```

### 1.3配置HTML模板

##### js文件打包好了,但是我们不可能每次在`html`文件中手动引入打包好的js。

> 这里可能有的朋友会认为我们打包js文件名称不是一直是固定的嘛(output.js)？这样每次就不用改动引入文件名称了呀？实际上我们日常开发中往往会这样配置:

```
module.export={
	//省略其他配置
	output:{
		filename:'[name].[hash:8].js'  //打包后的文件名称
		path:path.resolve(__dirname,'../dist') //打包后的目录
	}
}
```

##### 这时候生成的dist目录文件 如下

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p3.png?token=AMCTPY6WYOFTRLJAEKFRUY26AXEQ4)

##### 为了缓存，你会发现打包好的js文件的名称每次都不一样。webpack打包出来的js文件我们需要引入到html中，但是每次我们都手动修改js文件名显得很麻烦，因此我们需要一个插件来帮我们完成这件事情:

```
npm i -D html-webpack-plugin
```

##### 新建一个build同级的文件夹public，里面新建一个index.html，具体配置文件如下

```javascript
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development', // 开发模式
    entry: path.resolve(__dirname,'../src/main.js'),    // 入口文件
    output: {
      filename: '[name].[hash:8].js',      // 打包后的文件名称
      path: path.resolve(__dirname,'../dist')  // 打包后的目录
    },
    plugins:[
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/index.html')
      })
    ]
}
```

##### 生成目录如下：

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p4.png?token=AMCTPY6VZEPGCF6XEU5QRUC6AXES4)

##### 可以发现打包生成的js文件已经被自动引入html文件中

### 1.3.1多入口文件如何开发

> 生成多个html-webpack-plugin实例来解决这个问题

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development', // 开发模式
    entry: {
      main:path.resolve(__dirname,'../src/main.js'),
      header:path.resolve(__dirname,'../src/header.js')
  }, 
    output: {
      filename: '[name].[hash:8].js',      // 打包后的文件名称
      path: path.resolve(__dirname,'../dist')  // 打包后的目录
    },
    plugins:[
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/index.html'),
        filename:'index.html',
        chunks:['main'] // 与入口文件对应的模块名
      }),
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/header.html'),
        filename:'header.html',
        chunks:['header'] // 与入口文件对应的模块名
      }),
    ]
}

```

##### 此时会发现生成以下目录

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p5.png?token=AMCTPYZEV54EYSJL6CX6CNS6AXEUQ)

### 1.3.2clean-webpack-plugin

> 每次执行npm run build 会发现dist文件夹里会残留上次打包的文件，这里我们推荐一个plugin来帮我们在打包输出前清空文件夹clean-webpack-plugin

```javascript
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
module.exports = {
    // ...省略其他配置
    plugins:[new CleanWebpackPlugin()]
}
```

### 1.4引用CSS

##### 我们的入口文件是js，所以我们在入口js中引入我们的css文件

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p6.png?token=AMCTPY6FT65WFDECQJGX2LS6AXEZK)

##### 如果我们使用less来构建样式，则需要多安装两个

```
npm i -D less less-loader
```

##### 配置文件如下

```javascript
// webpack.config.js
module.exports = {
    // ...省略其他配置
    module:{
      rules:[
        {
          test:/\.css$/,
          use:['style-loader','css-loader'] // 从右向左解析原则
        },
        {
          test:/\.less$/,
          use:['style-loader','css-loader','less-loader'] // 从右向左解析原则
        }
      ]
    }
} 

```

##### 浏览器打开html如下

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/p7.png?token=AMCTPY2EJZAP6REJEONFWGK6AXE3Y)

### 1.4.1为css添加浏览器前缀

```
npm i -D postcss-loader autoprefixer
```

##### 配置如下

```javascript
// webpack.config.js
module.exports = {
    module:{
        rules:[
            test/\.less$/,
            use:['style-loader','css-loader','postcss-loader','less-loader'] // 从右向左解析原则
        ]
    }
} 
```

##### 接下来，我们还需要引入`autoprefixer`使其生效,这里有两种方式

##### 1、在项目根目录下创建一个postcss.config.js文件，配置如下：

```javascript
module.exports={
	plugins:[require('autoprefixer')] //引用该插件即可了
}
```

##### 2、直接在webpack.config.js里配置

```javascript
// webpack.config.js
module.exports = {
    //...省略其他配置
    module:{
        rules:[{
            test:/\.less$/,
            use:['style-loader','css-loader',{
                loader:'postcss-loader',
                options:{
                    plugins:[require('autoprefixer')]
                }
            },'less-loader'] // 从右向左解析原则
        }]
    }
}
```

##### 这时候我们发现css通过style标签的方式添加到了html文件中，但是如果样式文件很多，全部添加到html中，难免显得混乱。这时候我们想用把css拆分出来用外链的形式引入css文件怎么做呢？这时候我们就需要借助插件来帮助我们

### 1.4.2拆分css

```
npm i -D mini-css-extract-plugin
```

> webpack 4.0以前，我们通过`extract-text-webpack-plugin`插件，把css样式从js文件中提取到单独的css文件中。webpack4.0以后，官方推荐使用`mini-css-extract-plugin`插件来打包css文件

##### 配合文件如下

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  //...省略其他配置
  module: {
    rules: [
      {
        test: /\.less$/,
        use: [
           MiniCssExtractPlugin.loader,
          'css-loader',
          'less-loader'
        ],
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
        filename: "[name].[hash].css",
        chunkFilename: "[id].css",
    })
  ]
}
```

### 1.4.3拆分多个css

>这里需要说的细一点,上面我们所用到的`mini-css-extract-plugin`会将所有的css样式合并为一个css文件。如果你想拆分为一一对应的多个css文件,我们需要使用到`extract-text-webpack-plugin`，而目前`mini-css-extract-plugin`还不支持此功能。我们需要安装@next版本的`extract-text-webpack-plugin`

```
npm i -D extract-text-webpack-plugin@next
```

```javascript
// webpack.config.js

const path = require('path');
const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin')
let indexLess = new ExtractTextWebpackPlugin('index.less');
let indexCss = new ExtractTextWebpackPlugin('index.css');
module.exports = {
    module:{
      rules:[
        {
          test:/\.css$/,
          use: indexCss.extract({
            use: ['css-loader']
          })
        },
        {
          test:/\.less$/,
          use: indexLess.extract({
            use: ['css-loader','less-loader']
          })
        }
      ]
    },
    plugins:[
      indexLess,
      indexCss
    ]
}

```

### 1.5打包 图片、字体、媒体、等文件

##### `file-loader`就是将文件在进行一些处理后（主要是处理文件名和路径、解析文件url），并将文件移动到输出的目录中

##### `url-loader` 一般与`file-loader`搭配使用，功能与 file-loader 类似，如果文件小于限制的大小。则会返回 base64 编码，否则使用 file-loader 将文件移动到输出的目录中

```javascript
// webpack.config.js
module.exports = {
  // 省略其它配置 ...
  module: {
    rules: [
      // ...
      {
        test: /\.(jpe?g|png|gif)$/i, //图片文件
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                    name: 'img/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/, //媒体文件
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                  name: 'media/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/i, // 字体
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240,
              fallback: {
                loader: 'file-loader',
                options: {
                  name: 'fonts/[name].[hash:8].[ext]'
                }
              }
            }
          }
        ]
      },
    ]
  }
}

```

### 1.6用babel转义js文件

##### 为了使我们的js代码兼容更多的环境我们需要安装依赖

```
npm i babel-loader @babel/preset-env @babel/core
```

- 注意babel-loader与babel-core的版本对应关系

1、bbael-loader 8.x对应babel-core 7.x

2、babel-loader 7.x对应babel-core 6.x

##### 配置如下

```javascript
// webpack.config.js
module.exports = {
    // 省略其它配置 ...
    module:{
        rules:[
          {
            test:/\.js$/,
            use:{
              loader:'babel-loader',
              options:{
                presets:['@babel/preset-env']
              }
            },
            exclude:/node_modules/
          },
       ]
    }
}
```

##### 上面的`babel-loader`只会将 ES6/7/8语法转换为ES5语法，但是对新api并不会转换 例如(promise、Generator、Set、Maps、Proxy等),此时我们需要借助babel-polyfill来帮助我们转换

```
npm i @babel/polyfill
```

```javascript
// webpack.config.js
const path = require('path')
module.exports = {
    entry: ["@babel/polyfill,path.resolve(__dirname,'../src/index.js')"],    // 入口文件
}
```

## 搭建vue开发环境

##### 上面的小例子已经帮助而我们实现了打包css、图片、js、html等文件。但是我们还需要以下几种配置：

### 2.1解析.vue文件

```
npm i -D vue-loader vue-template-compiler vue-style-loader
npm i -S vue
```

##### vue-loader 用于解析.vue文件

##### vue-template-compiler用于编译模板 配置如下

```javascript
const vueLoaderPlugin = require('vue-loader/lib/plugin')
module.exports = {
    module:{
        rules:[{
            test:/\.vue$/,
            use:['vue-loader']
        },]
     },
    resolve:{
        alias:{
          'vue$':'vue/dist/vue.runtime.esm.js',
          ' @':path.resolve(__dirname,'../src')
        },
        extensions:['*','.js','.json','.vue']
   },
   plugins:[
        new vueLoaderPlugin()
   ]
}
```

### 2.2配置webpack-dev-server进行热更新

```
npm i -D webpack-dev-server
```

##### 配置如下

```
const Webpack = require('webpack')
module.exports = {
  // ...省略其他配置
  devServer:{
    port:3000,
    hot:true,
    contentBase:'../dist'
  },
  plugins:[
    new Webpack.HotModuleReplacementPlugin()
  ]
}
```

##### 完整配置如下

```javascript
// webpack.config.js
const path = require('path');
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin')
const vueLoaderPlugin = require('vue-loader/lib/plugin')
const Webpack = require('webpack')
module.exports = {
    mode:'development', // 开发模式
    entry: {
      main:path.resolve(__dirname,'../src/main.js'),
    }, 
    output: {
      filename: '[name].[hash:8].js',      // 打包后的文件名称
      path: path.resolve(__dirname,'../dist')  // 打包后的目录
    },
    module:{
      rules:[
        {
          test:/\.vue$/,
          use:['vue-loader']
        },
        {
          test:/\.js$/,
          use:{
            loader:'babel-loader',
            options:{
              presets:[
                ['@babel/preset-env']
              ]
            }
          }
        },
        {
          test:/\.css$/,
          use: ['vue-style-loader','css-loader',{
            loader:'postcss-loader',
            options:{
              plugins:[require('autoprefixer')]
            }
          }]
        },
        {
          test:/\.less$/,
          use: ['vue-style-loader','css-loader',{
            loader:'postcss-loader',
            options:{
              plugins:[require('autoprefixer')]
            }
          },'less-loader']
        }
      ]
    },
    resolve:{
      alias:{
        'vue$':'vue/dist/vue.runtime.esm.js',
        ' @':path.resolve(__dirname,'../src')
      },
      extensions:['*','.js','.json','.vue']
    },
    devServer:{
      port:3000,
      hot:true,
      contentBase:'../dist'
    },
    plugins:[
      new CleanWebpackPlugin(),
      new HtmlWebpackPlugin({
        template:path.resolve(__dirname,'../public/index.html'),
        filename:'index.html'
      }),
      new vueLoaderPlugin(),
      new Webpack.HotModuleReplacementPlugin()
    ]
}
```

### 2.3配置打包命令

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/w8.png?token=AMCTPY7W7IUQGJ3PKHDR43S6AXE7I)

##### 打包文件已经配置完毕，接下来让我们测试一下

##### 首先在src新建一个main.js

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/w9.png?token=AMCTPY6Y7KI6DZ4ID7UVRCC6AXFBI)

##### 新建一个App.vue

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/w10.png?token=AMCTPY44LBWK7FQ2OGDCDN26AXFCO)

##### 新建一个public文件夹，里面新建一个index.html

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/w11.png?token=AMCTPY3IAKWOWSS75TBQK2C6AXFFS)

##### 执行`npm run dev`这时候如果浏览器出现Vue开发环境运行成功，那么恭喜你，已经成功迈出了第一步！

### 2.4区分开发环境与生产环境

##### 实际应用到项目中，我们需要区分开发环境与生产环境，我们在原来webpack.config.js的基础上再新增两个文件

- webpack.dev.js 开发环境配置文件

  ```
  开发环境主要实现的是热更新，不要压缩代码，完整的sourceMap
  ```

- webpack.prod.js生产环境配置文件

  ```
  生产环境主要实现的是压缩代码、提取css文件、合理的sourceMap、分割代码
  需要安装以下模块:
  npm i -D  webpack-merge copy-webpack-plugin optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin
  ```

- `webpack-merge` 合并配置
- `copy-webpack-plugin` 拷贝静态资源
- `optimize-css-assets-webpack-plugin` 压缩css
- `uglifyjs-webpack-plugin` 压缩js

>`webpack mode`设置`production`的时候会自动压缩js代码。原则上不需要引入`uglifyjs-webpack-plugin`进行重复工作。但是`optimize-css-assets-webpack-plugin`压缩css的同时会破坏原有的js压缩，所以这里我们引入`uglifyjs`进行压缩

### 2.4.1webpack.config.js

```javascript
const path = require('path')
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const vueLoaderPlugin = require('vue-loader/lib/plugin')
const MiniCssExtractPlugin = require("mini-css-extract-plugin")
const devMode = process.argv.indexOf('--mode=production') === -1;
module.exports = {
  entry:{
    main:path.resolve(__dirname,'../src/main.js')
  },
  output:{
    path:path.resolve(__dirname,'../dist'),
    filename:'js/[name].[hash:8].js',
    chunkFilename:'js/[name].[hash:8].js'
  },
  module:{
    rules:[
      {
        test:/\.js$/,
        use:{
          loader:'babel-loader',
          options:{
            presets:['@babel/preset-env']
          }
        },
        exclude:/node_modules/
      },
      {
        test:/\.vue$/,
        use:['cache-loader','thread-loader',{
          loader:'vue-loader',
          options:{
            compilerOptions:{
              preserveWhitespace:false
            }
          }
        }]
      },
      {
        test:/\.css$/,
        use:[{
          loader: devMode ? 'vue-style-loader' : MiniCssExtractPlugin.loader,
          options:{
            publicPath:"../dist/css/",
            hmr:devMode
          }
        },'css-loader',{
          loader:'postcss-loader',
          options:{
            plugins:[require('autoprefixer')]
          }
        }]
      },
      {
        test:/\.less$/,
        use:[{
          loader:devMode ? 'vue-style-loader' : MiniCssExtractPlugin.loader,
          options:{
            publicPath:"../dist/css/",
            hmr:devMode
          }
        },'css-loader','less-loader',{
          loader:'postcss-loader',
          options:{
            plugins:[require('autoprefixer')]
          }
        }]
      },
      {
        test:/\.(jep?g|png|gif)$/,
        use:{
          loader:'url-loader',
          options:{
            limit:10240,
            fallback:{
              loader:'file-loader',
              options:{
                name:'img/[name].[hash:8].[ext]'
              }
            }
          }
        }
      },
      {
        test:/\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
        use:{
          loader:'url-loader',
          options:{
            limit:10240,
            fallback:{
              loader:'file-loader',
              options:{
                name:'media/[name].[hash:8].[ext]'
              }
            }
          }
        }
      },
      {
        test:/\.(woff2?|eot|ttf|otf)(\?.*)?$/i,
        use:{
          loader:'url-loader',
          options:{
            limit:10240,
            fallback:{
              loader:'file-loader',
              options:{
                name:'media/[name].[hash:8].[ext]'
              }
            }
          }
        }
      }
    ]
  },
  resolve:{
    alias:{
      'vue$':'vue/dist/vue.runtime.esm.js',
      ' @':path.resolve(__dirname,'../src')
    },
    extensions:['*','.js','.json','.vue']
  },
  plugins:[
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template:path.resolve(__dirname,'../public/index.html')
    }),
    new vueLoaderPlugin(),
    new MiniCssExtractPlugin({
      filename: devMode ? '[name].css' : '[name].[hash].css',
      chunkFilename: devMode ? '[id].css' : '[id].[hash].css'
    })
  ]
}
```

### 2.4.2webpack.dev.js

```javascript
const Webpack = require('webpack')
const webpackConfig = require('./webpack.config.js')
const WebpackMerge = require('webpack-merge')
module.exports = WebpackMerge(webpackConfig,{
  mode:'development',
  devtool:'cheap-module-eval-source-map',
  devServer:{
    port:3000,
    hot:true,
    contentBase:'../dist'
  },
  plugins:[
    new Webpack.HotModuleReplacementPlugin()
  ]
})
```

### 2.4.3webpack.prod.js

```javascript
const path = require('path')
const webpackConfig = require('./webpack.config.js')
const WebpackMerge = require('webpack-merge')
const CopyWebpackPlugin = require('copy-webpack-plugin')
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin')
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
module.exports = WebpackMerge(webpackConfig,{
  mode:'production',
  devtool:'cheap-module-source-map',
  plugins:[
    new CopyWebpackPlugin([{
      from:path.resolve(__dirname,'../public'),
      to:path.resolve(__dirname,'../dist')
    }]),
  ],
  optimization:{
    minimizer:[
      new UglifyJsPlugin({//压缩js
        cache:true,
        parallel:true,
        sourceMap:true
    }),
    new OptimizeCssAssetsPlugin({})
    ],
    splitChunks:{
      chunks:'all',
      cacheGroups:{
        libs: {
          name: "chunk-libs",
          test: /[\\/]node_modules[\\/]/,
          priority: 10,
          chunks: "initial" // 只打包初始时依赖的第三方
        }
      }
    }
  }
})
```

### 2.5优化webpack配置

##### 优化配置对我们来说非常有实际意义，这实际关系到你打包出来文件的大小，打包的速度等。 具体优化可以分为以下几点：

### 2.5.1优化打包速度

> 构建速度指的是我们每次修改代码后热更新的速度以及发布前打包文件的速度。

### 2.5.1.1合理的配置mode参数与devtool参数

`mode`可设置`development`` production`两个参数
 如果没有设置，`webpack4` 会将 `mode` 的默认值设置为 `production` 
 `production`模式下会进行`tree shaking`(去除无用代码)和`uglifyjs`(代码压缩混淆)

### 2.5.1.2缩小文件的搜索范围（配置include exclude alias noParse extensions）

- `alias`: 当我们代码中出现 `import 'vue'`时， webpack会采用向上递归搜索的方式去`node_modules` 目录下找。为了减少搜索范围我们可以直接告诉webpack去哪个路径下查找。也就是别名(`alias`)的配置。
- `include exclude` 同样配置`include exclude`也可以减少`webpack loader`的搜索转换时间。
- `noParse ` 当我们代码中使用到`import jq from 'jquery'`时，`webpack`会去解析jq这个库是否有依赖其他的包。但是我们对类似`jquery`这类依赖库，一般会认为不会引用其他的包(特殊除外,自行判断)。增加`noParse`属性,告诉`webpack`不必解析，以此增加打包速度。
- `extensions ``webpack`会根据`extensions`定义的后缀查找文件(频率较高的文件类型优先写在前面)

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/carbon-2.png?token=AMCTPY6ONNVCSFA4DVFHLLS6AXFIE)

### 2.5.1.3使用HappyPack开启多进程Loader转换

>在webpack构建过程中，实际上耗费时间大多数用在loader解析转换以及代码的压缩中。日常开发中我们需要使用Loader对js，css，图片，字体等文件做转换操作，并且转换的文件数据量也是非常大。由于js单线程的特性使得这些转换操作不能并发处理文件，而是需要一个个文件进行处理。HappyPack的基本原理是将这部分任务分解到多个子进程中去并行处理，子进程处理完成后把结果发送到主进程中，从而减少总的构建时间

```
npm i -D happypack
```

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/carbon-3.png?token=AMCTPY5QB3UBYVSOU4Q7RCK6AXFJE)

### 2.5.1.4使用webpack-parallel-uglify-plugin增强代码压缩

> 上面对于loader转换已经做优化，那么下面还有另一个难点就是优化代码的压缩时间。

```
npm i -D webpack-parallel-uglify-plugin
```

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/carbon-4.png?token=AMCTPY5KDI7PHB26LMRZ35K6AXFLK)

### 2.5.1.5抽离第三方模块

>对于开发项目中不经常会变更的静态依赖文件。类似于我们的`elementUi、vue`全家桶等等。因为很少会变更，所以我们不希望这些依赖要被集成到每一次的构建逻辑中去。 这样做的好处是每次更改我本地代码的文件的时候，`webpack`只需要打包我项目本身的文件代码，而不会再去编译第三方库。以后只要我们不升级第三方包的时候，那么`webpack`就不会对这些库去打包，这样可以快速的提高打包的速度。

##### 这里我们使用`webpack`内置的`DllPlugin DllReferencePlugin`进行抽离在与`webpack`配置文件同级目录下新建`webpack.dll.config.js` 代码如下

```javascript
// webpack.dll.config.js
const path = require("path");
const webpack = require("webpack");
module.exports = {
  // 你想要打包的模块的数组
  entry: {
    vendor: ['vue','element-ui'] 
  },
  output: {
    path: path.resolve(__dirname, 'static/js'), // 打包后文件输出的位置
    filename: '[name].dll.js',
    library: '[name]_library' 
     // 这里需要和webpack.DllPlugin中的`name: '[name]_library',`保持一致。
  },
  plugins: [
    new webpack.DllPlugin({
      path: path.resolve(__dirname, '[name]-manifest.json'),
      name: '[name]_library', 
      context: __dirname
    })
  ]
};

```

##### 在package.json中配置如下命令

```json
"dll":"webpack --config build/webpack.dll.config.js"
```

##### 接下来在我们的webpack.config.js中增加以下代码

```javascript
module.exports = {
  plugins: [
    new webpack.DllReferencePlugin({
      context: __dirname,
      manifest: require('./vendor-manifest.json')
    }),
    new CopyWebpackPlugin([ // 拷贝生成的文件到dist目录 这样每次不必手动去cv
      {from: 'static', to:'static'}
    ]),
  ]
};
```

执行

```
npm run dll
```

##### 会发现生成了我们需要的集合第三地方 代码的`vendor.dll.js` 我们需要在`html`文件中手动引入这个`js`文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>hello webpack</title>
  <script src="static/js/vendor.dll.js"></script>
</head>
<body>
  <div id="app"></div>
</body>
</html>

```

##### 这样如果我们没有更新第三方依赖包，就不必`npm run dll`。直接执行`npm run dev npm run build`的时候会发现我们的打包速度明显有所提升。因为我们已经通过`dllPlugin`将第三方依赖包抽离出来了

### 2.5.1.6配置缓存

>我们每次执行构建都会把所有的文件都重复编译一遍，这样的重复工作是否可以被缓存下来呢，答案是可以的，目前大部分 `loader` 都提供了`cache` 配置项。比如在 `babel-loader` 中，可以通过设置`cacheDirectory` 来开启缓存，`babel-loader?cacheDirectory=true` 就会将每次的编译结果写进硬盘文件（默认是在项目根目录下的`node_modules/.cache/babel-loader`目录内，当然你也可以自定义）

##### 但如果 `loader` 不支持缓存呢？我们也有方法,我们可以通过`cache-loader` ，它所做的事情很简单，就是 `babel-loader` 开启 `cache `后做的事情，将 `loader` 的编译结果写入硬盘缓存。再次构建会先比较一下，如果文件较之前的没有发生变化则会直接使用缓存。使用方法如官方 demo 所示，在一些性能开销较大的 loader 之前添加此 loader即可

```
npm i -D cache-loader
```

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/carbon-5.png?token=AMCTPY345PGYFDVH4ZM4BZ26AXFR4)

### 2.5.2优化打包文件体积

> 打包的速度我们是进行优化，但是打包后的文件体积确实十分大，造成了页面加载缓慢，浪费流量等，接下来我们从文件体积上继续优化

### 2.5.2.1引入webpack-bundle-analyzer分析打包后的文件

##### webpack-bundle-analyzer将打包后的内容束展示为方便交互的直观树状图，让我们知道我们所构建包中真正引入的内容

```
npm i -D webpack-bunble-analyzer
```

![avtvor](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/webpack/carbon-6.png?token=AMCTPY7JQYAYZ76Y4J7UWAC6AXFUI)

##### 接下来在package.json里配置启动命令

```
"analyz": "NODE_ENV=production npm_config_report=true npm run build" 
```

##### windows请安装npm i -D cross-env

```
"analyz": "cross-env NODE_ENV=production npm_config_report=true npm run build" 
```

##### 接下来npm run analyz浏览器会自动打开文件依赖图的网页

##### 2.5.2.3externals

>按照官方文档的解释，如果我们想引用一个库，但是又不想让`webpack`打包，并且又不影响我们在程序中以`CMD、AMD`或者`window/global`全局等方式进行使用，那就可以通过配置`Externals`。这个功能主要是用在创建一个库的时候用的，但是也可以在我们项目开发中充分使用 `Externals`的方式，我们将这些不需要打包的静态资源从构建逻辑中剔除出去，而使用 `CDN` 的方式，去引用它们。

##### 有时我们希望我们通过`script`引入的库，如用CDN的方式引入的`jquery`，我们在使用时，依旧用`require`的方式来使用，但是却不希望`webpack`将它又编译进文件中。这里官网案例已经足够清晰明了，大家有兴趣可以点击了解

```html
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous">
</script>
```

```javascript
module.exports = {
  //...
  externals: {
    jquery: 'jQuery'
  }
};
```

```
import $ from 'jquery';
$('.my-element').animate(/* ... */);
```

### 2.5.2.3 Tree-shaking

>这里单独提一下`tree-shaking`,是因为这里有个坑。`tree-shaking`的主要作用是用来清除代码中无用的部分。目前在`webpack4` 我们设置`mode`为`production`的时候已经自动开启了`tree-shaking`。但是要想使其生效，生成的代码必须是ES6模块。不能使用其它类型的模块如`CommonJS`之流。如果使用`Babel`的话，这里有一个小问题，因为`Babel`的预案（preset）默认会将任何模块类型都转译成`CommonJS`类型。修正这个问题也很简单，在`.babelrc`文件或在`webpack.config.js`文件中设置`modules： false`就好了

```javascript
// .babelrc
{
  "presets": [
    ["@babel/preset-env",
      {
        "modules": false
      }
    ]
  ]
}
```

##### 或者

```javascript
// webpack.config.js

module: {
    rules: [
        {
            test: /\.js$/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: ['@babel/preset-env', { modules: false }]
                }
            }，
            exclude: /(node_modules)/
        }
    ]
}

```

### 3手写webpack系列

> 经历过上面两个部分，我们已经可以熟练的运用相关的loader和plugin对我们的代码进行转换、解析。接下来我们自己手动实现loader与plugin，使其在平时的开发中获得更多的乐趣。

### 3.1手写webpack loader

> `loader`从本质上来说其实就是一个`node`模块。相当于一台榨汁机`(loader)`将相关类型的文件代码`(code)`给它。根据我们设置的规则，经过它的一系列加工后还给我们加工好的果汁`(code)`。

##### loader编写原则

- 单一原则: 每个 `Loader` 只做一件事；
- 链式调用: `Webpack` 会按顺序链式调用每个 `Loader`；
- 统一原则: 遵循 `Webpack` 制定的设计规则和结构，输入与输出均为字符串，各个 `Loader` 完全独立，即插即用；

##### 在日常开发环境中，为了方便调试我们往往会加入许多`console`打印。但是我们不希望在生产环境中存在打印的值。那么这里我们自己实现一个`loader`去除代码中的`console`

```
知识点普及之AST。AST通俗的来说，假设我们有一个文件a.js,我们对a.js里面的1000行进行一些操作处理,比如为所有的await 增加try catch,以及其他操作，但是a.js里面的代码本质上来说就是一堆字符串。那我们怎么办呢，那就是转换为带标记信息的对象(抽象语法树)我们方便进行增删改查。这个带标记的对象(抽象语法树)就是AST。这里推荐一篇不错的AST文章 AST快速入门
https://segmentfault.com/a/1190000016231512
```

```
npm i -D @babel/parser @babel/traverse @babel/generator @babel/types
```

```
@babel/parser` 将源代码解析成 `AST
```

- `@babel/traverse` 对`AST`节点进行递归遍历，生成一个便于操作、转换的`path`对象

- `@babel/generator` 将`AST`解码生成`js`代码

- `@babel/types`通过该模块对具体的`AST`节点进行进行增、删、改、查

##### 新建drop-console.js

```javascript
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default
const generator = require('@babel/generator').default
const t = require('@babel/types')
module.exports=function(source){
  const ast = parser.parse(source,{ sourceType: 'module'})
  traverse(ast,{
    CallExpression(path){ 
      if(t.isMemberExpression(path.node.callee) && t.isIdentifier(path.node.callee.object, {name: "console"})){
        path.remove()
      }
    }
  })
  const output = generator(ast, {}, source);
  return output.code
}
```

##### 如何使用

```javascript
const path = require('path')
module.exports = {
  mode:'development',
  entry:path.resolve(__dirname,'index.js'),
  output:{
    filename:'[name].[contenthash].js',
    path:path.resolve(__dirname,'dist')
  },
  module:{
    rules:[{
      test:/\.js$/,
      use:path.resolve(__dirname,'drop-console.js')
      }
    ]
  }
}
```

> 实际上在`webpack4`中已经集成了去除`console`功能，在`minimizer`中可配置 [去除console](https://webpack.js.org/plugins/terser-webpack-plugin/#root)

##### 附上官网 [如何编写一个loader](https://webpack.docschina.org/contribute/writing-a-loader/)

### 3.2手写webpack plugin

>在 `Webpack` 运行的生命周期中会广播出许多事件，`Plugin` 可以监听这些事件，在合适的时机通过`Webpack`提供的`API`改变输出结果。通俗来说：一盘美味的 [盐豆炒鸡蛋](https://www.meishij.net/zuofa/yandouchaojidan.html) 需要经历烧油 炒制 调味到最后的装盘等过程，而`plugin`相当于可以监控每个环节并进行操作，比如可以写一个少放胡椒粉`plugin`,监控`webpack`暴露出的生命周期事件(调味)，在调味的时候执行少放胡椒粉操作。那么它与`loader`的区别是什么呢？上面我们也提到了`loader`的单一原则,`loader`只能一件事，比如说`less-loader`,只能解析`less`文件，`plugin`则是针对整个流程执行广泛的任务。

##### 一个基本的plugin插件结构如下

```javascript
class firstPlugin {
  constructor (options) {
    console.log('firstPlugin options', options)
  }
  apply (compiler) {
    compiler.plugin('done', compilation => {
      console.log('firstPlugin')
    ))
  }
}

module.exports = firstPlugin

```

> compiler/compilation是什么？

- `compiler` 对象包含了`Webpack` 环境所有的的配置信息。这个对象在启动 `webpack` 时被一次性建立，并配置好所有可操作的设置，包括 `options`，`loader` 和 `plugin`。当在 `webpack` 环境中应用一个插件时，插件将收到此 `compiler` 对象的引用。可以使用它来访问 `webpack` 的主环境。
- `compilation`对象包含了当前的模块资源、编译生成资源、变化的文件等。当运行`webpack` 开发环境中间件时，每当检测到一个文件变化，就会创建一个新的 `compilation`，从而生成一组新的编译资源。`compilation` 对象也提供了很多关键时机的回调，以供插件做自定义处理时选择使用。

#### **compiler和 compilation的区别在于**

- compiler代表了整个webpack从启动到关闭的生命周期，而compilation 只是代表了一次新的编译过程
- compiler和compilation暴露出许多钩子，我们可以根据实际需求的场景进行自定义处理

##### [compiler钩子文档](https://www.webpackjs.com/api/compiler-hooks/)

##### [compilation钩子文档](https://www.webpackjs.com/api/compilation-hooks/)

##### 下面我们手动开发一个简单的需求,在生成打包文件之前自动生成一个关于打包出文件的大小信息

##### 新建一个`webpack-firstPlugin.js`

```javascript
class firstPlugin{
  constructor(options){
    this.options = options
  }
  apply(compiler){
    compiler.plugin('emit',(compilation,callback)=>{
      let str = ''
      for (let filename in compilation.assets){
        str += `文件:${filename}  大小${compilation.assets[filename]['size']()}\n`
      }
      // 通过compilation.assets可以获取打包后静态资源信息，同样也可以写入资源
      compilation.assets['fileSize.md'] = {
        source:function(){
          return str
        },
        size:function(){
          return str.length
        }
      }
      callback()
    })
  }
}
module.exports = firstPlugin
```

##### 如何使用

```
const path = require('path')
const firstPlugin = require('webpack-firstPlugin.js')
module.exports = {
    // 省略其他代码
    plugins:[
        new firstPlugin()
    ]
}
```

```
执行 npm run build即可看到在dist文件夹中生成了一个包含打包文件信息的fileSize.md
```

> 上面两个`loader`与`plugin`案例只是一个引导，实际开发需求中的`loader`与`plugin`要考虑的方面很多，建议大家自己多动手尝试一下。

##### 附上官网 [如何编写一个plugin](https://www.webpackjs.com/contribute/writing-a-plugin/)



### 原文链接：https://juejin.im/post/5de87444518825124c50cd36

### 参考文献：https://www.webpackjs.com/
