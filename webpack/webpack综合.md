# webpack

## 一： webpack是什么

**一种前端资源构建工具**

> 在前端工程中，less文件、或通过`import`导入的依赖，浏览器无法直接解析，因此需要单独对这些文件进行维护
>
> 当这种类型的文件过多时，就希望有一个工具可以统一维护这些类型的文件
>
> 因此：`webpack`  这一类的构建工具，应运而生

**一个静态模块打包器（module bundler）**

> 在 `webpack` 看来，前端所有的资源文件（js/json/css/img/less/...）都会作为模块处理，他将根据模块的依赖关系进行静态解析，打包生成对应的静态资源（bundle）

![1634375544050](E:/file/typora_img/1634375544050.png)



## 二：核心概念

### 1. 入口（entry）

> 指示 `webpack` 以哪个文件为入口起点开始打包，分析构建以来图
>
> **入口** 指示 `webpack` 应该使用哪个模块，来作为构建其内部 **依赖图 **的开始。进入入口起点后，`webpack` 会找出有哪些模块和库是入口起点（直接和间接）依赖的。
>
> 默认值是 `./src/index.js`，但你可以通过在配置中的 `entry` 属性，来指定一个（或多个）不同的入口起点

**webpack.config.js** 

~~~js
module.exports = {
  entry: './path/to/my/entry/file.js',
};
~~~

### 2.输出（output）

> **output** 属性告诉 `webpack` 在哪里输出它所创建的 *bundle*，以及如何命名这些文件。主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中。
>
> 你可以通过在配置中指定一个 `output` 字段，来配置这些处理过程：

**webpack.config.js**

~~~js
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
};
~~~

 在上面的示例中，我们通过 `output.filename` 和 `output.path` 属性，来告诉 webpack bundle 的名称，以及我们想要 bundle 生成(emit)到哪里。 

### 3. loader

>  `webpack`  只能理解 `JavaScript` 和 `JSON`  文件，这是` webpack` 开箱可用的自带能力。**loader** 让 `webpack` 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。 

在更高层面，在 webpack 的配置中，**loader** 有两个属性：

1. `test` 属性，识别出哪些文件会被转换。
2. `use` 属性，定义出在进行转换时，应该使用哪个 loader。

**webpack.config.js** 

~~~js
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
};
~~~

 以上配置中，对一个单独的 module 对象定义了 `rules` 属性，里面包含两个必须属性：`test` 和 `use`。这告诉 ` webpack`  编译器(compiler) 如下信息： 

> “嘿，webpack 编译器，当你碰到「在 `require()`/`import` 语句中被解析为 '.txt' 的路径」时，在你对它打包之前，先 **use(使用)** `raw-loader` 转换一下。”

**Warning**

1.  重要的是要记住，在 webpack 配置中定义 rules 时，要定义在 `module.rules` 而不是 `rules` 中 
2.  使用正则表达式匹配文件时，你不要为它添加引号。也就是说，`/\.txt$/` 与 `'/\.txt$/'` 或 `"/\.txt$/"` 不一样。前者指示 webpack 匹配任何以 .txt 结尾的文件，后者指示 webpack 匹配具有绝对路径 '.txt' 的单个文件; 

### 4. 插件（plugin）

>  loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量 

 想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建一个插件实例。 

 **webpack.config.js** 

~~~js
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

module.exports = {
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
  plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
~~~

 在上面的示例中，`html-webpack-plugin` 为应用程序生成一个 HTML 文件，并自动将生成的所有 bundle 注入到此文件中。 

### 5. 模式（mode）

 通过选择 `development`, `production` 或 `none` 之中的一个，来设置 `mode` 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 `production`。 

 **webpack.config.js** 

~~~js
module.exports = {
  mode: 'production',
};
~~~



## 三：webpack初体验

### 1.基本使用

* 初始化项目

~~~bash
cd webpack-test
npm init
~~~

* 安装webpack

~~~bash
npm install webpack webpack-cli -g  // 在项目中全局webpack
~~~

* 创建入口文件

在项目根目录下，创建src目录，并在src目录中创建入口文件index.js

~~~js
function add(x, y) {
    return x + y
}

console.log(add(1,2))
~~~

* 执行打包命令

~~~js
/*
    打包命令：
    1.开发： webpack ./src/index.js -o ./src/build --mode=development
        webpack会以 ./src/index.js 为入口文件，打包生成的文件输出到 ./src/build/built.js 整体打包环境为：开发环境
    1.生产： webpack ./src/index.js -o ./src/build --mode=production
        webpack会以 ./src/index.js 为入口文件，打包生成的文件输出到 ./src/build/built.js 整体打包环境为：生产环境
    
*/
~~~

* 生成打包结果

在./src/build/目录下生成打包后的文件



### 2. 打包样式资源

* **打包css文件**

  * **安装相关loader**

  ~~~bash
  npm install css-loader style-loader
  ~~~

  * **webpack.config.js**

  ~~~js
  /*
   webpack.config.js webpack配置文件
   作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
  
   所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
  */
  
  // resolve 用来拼接绝对路径方法
  const { resolve } = require('path')
  
  module.exports = {
  	// webpack 配置
  	// 入口起点
  	entry: './src/index.js',
  	// 输出路径
  	// __dirname nodejs的变量，代表当前文件的目录绝对路径
  	output: {
  		path: resolve(__dirname, 'dist')
  	},
  	// loader配置
  	module: {
  		rules: [
              {
                  // 详细loader配置
                  test: /\.css$/,
                  use: [
                      // use数组中的loader执行顺序：从右到左，从下到上
                      // 创建style标签，将js中得到样式资源插入，添加到header中生效
                      {loader: 'style-loader'},
                      // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                      {loader: 'css-loader'}
                  ]
              },
  		],
  	},
  	// plugin的配置
  	plugins: [
  		// 详细配置
  	],
  	// 模式
  	mode: 'development'
  	// mode: 'production' 
  }
  ~~~

* **打包lsee/css文件**

  * **安装相关loader**

    ~~~bash
    npm install css-loader less-loader less style-loader 
    ~~~

  * **webpack.config.js**

    ~~~js
    /*
     webpack.config.js webpack配置文件
     作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
    
     所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
    */
    
    // resolve 用来拼接绝对路径方法
    const { resolve } = require('path')
    
    module.exports = {
    	// webpack 配置
    	// 入口起点
    	entry: './src/index.js',
    	// 输出路径
    	// __dirname nodejs的变量，代表当前文件的目录绝对路径
    	output: {
    		path: resolve(__dirname, 'dist')
    	},
    	// loader配置
    	module: {
    		rules: [
                {
                    // 详细loader配置
                    test: /\.css$/,
                    use: [
                        // use数组中的loader执行顺序：从右到左，从下到上
                        // 创建style标签，将js中得到样式资源插入，添加到header中生效
                        {loader: 'style-loader'},
                        // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                        {loader: 'css-loader'}
                    ]
                },
                {
                    // 详细loader配置
                    test: /\.less$/,
                    use: [
                        // use数组中的loader执行顺序：从右到左，从下到上
                        // 创建style标签，将js中得到样式资源插入，添加到header中生效
                        {loader: 'style-loader'},
                        // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                        {loader: 'css-loader'},
                        // 将less文件编译成css文件
                        {loader: 'less-loader'},
                    ]
                }
    		],
    	},
    	// plugin的配置
    	plugins: [
    		// 详细配置
    	],
    	// 模式
    	mode: 'development'
    	// mode: 'production' 
    }
    ~~~

### 3.打包html资源

* **安装插件**
* **webpack.config.js**

~~~js
/*
 webpack.config.js webpack配置文件
 作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）

 所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
*/

const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	// webpack 配置
	// 入口起点
	entry: './src/index.js',
	// 输出路径
	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
	output: {
		path: resolve(__dirname, 'dist')
	},
	// loader配置
	module: {
		rules: [
            {
                // 详细loader配置
            },
		],
	},
	// plugin的配置
	plugins: [
		// 详细配置
        /**
         * 需求：以某个html文件为模板，打包生成html文件
         * 实现：
         *  1. 通过npm安装：html-webpack-plugin
         *  2. 引入该插件： const HtmlWebpackPlugin = require('html-webpack-plugin')
         *  3. 默认创建一个空html,自动引入打包输出的所有资源（若有template参数，则以此为模板生成html文件）
         */
        new HtmlWebpackPlugin({ 
            // 复制 ./static/index.html 文件，并自动引入打包输出的所有资源（js/css）
            template: './static/index.html',
            minify: { // 压缩HTML配置
                collapaseWhitespace: true, // 移除空格
                removeComments: true  // 移除注释
            }
        })
	],
	// 模式
	mode: 'development'
	// mode: 'production' 
}
~~~

### 4.打包图片资源

* **图片在css样式中引入**

  * **webpack 5+**

  > 在webpack 5+ 中直接在css中添加图片，打包即可生成图片

  * **webpack 4**

    * 安装loader

      ~~~bash
      npm install file-loader url-loader
      ~~~

    * **webpack-config.js**配置如下

    ~~~js
    /*
     webpack.config.js webpack配置文件
     作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
    
     所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
    */
    
    // resolve 用来拼接绝对路径方法
    const { resolve } = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    
    module.exports = {
    	// webpack 配置
    	// 入口起点
    	entry: './src/index.js',
    	// 输出路径
    	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
    	output: {
    		path: resolve(__dirname, 'dist')
    	},
    	// loader配置
    	module: {
    		rules: [
                {
                    // 详细loader配置
                    test: /\.css$/,
                    use: [
                        // use数组中的loader执行顺序：从右到左，从下到上
                        // 创建style标签，将js中得到样式资源插入，添加到header中生效
                        {loader: 'style-loader'},
                        // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                        {loader: 'css-loader'}
                    ]
                },
                {
                    // 详细loader配置
                    test: /\.(jpg|png|gif|svg)$/,
                    use: [
                        {
                            loader: 'url-loader',
                            options: {
                                /**
                                 * limit 参数说明：
                                 *  1. 图片小于8kb,就会被处理成base64
                                 *  2. 优点：减少请求数量（减轻服务器压力）
                                 *  3. 缺点：图片体积会更大（降低请求速度） 
                                 */
                                limit: 8 * 1024
                            }
                        }
                    ]
                },
    		],
    	},
    	// plugin的配置
    	plugins: [
    		// 详细配置
            /**
             * 需求：以某个html文件为模板，打包生成html文件
             * 实现：
             *  1. 通过npm安装：html-webpack-plugin
             *  2. 引入该插件： const HtmlWebpackPlugin = require('html-webpack-plugin')
             *  3. 默认创建一个空html,自动引入打包输出的所有资源（若有template参数，则以此为模板生成html文件）
             */
            new HtmlWebpackPlugin({ 
                // 复制 ./static/index.html 文件，并自动引入打包输出的所有资源（js/css）
                template: './static/index.html' 
            })
    	],
    	// 模式
    	mode: 'development'
    	// mode: 'production' 
    }
    ~~~

* **图片在html标签中引入**

  * **安装loader**

    ~~~bash
    npm install html-loader
    ~~~

    

  * **webpack.config.js**

    ~~~js
    /*
     webpack.config.js webpack配置文件
     作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
    
     所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
    */
    
    // resolve 用来拼接绝对路径方法
    const { resolve } = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    
    module.exports = {
    	// webpack 配置
    	// 入口起点
    	entry: './src/index.js',
    	// 输出路径
    	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
    	output: {
    		path: resolve(__dirname, 'dist')
    	},
    	// loader配置
    	module: {
    		rules: [
                {
                    // 详细loader配置
                    test: /\.css$/,
                    use: [
                        {loader: 'style-loader'},
                        {loader: 'css-loader'}
                    ]
                },
                {
                    // 详细loader配置
                    test: /\.less$/,
                    use: [
                        {loader: 'style-loader'},
                        {loader: 'css-loader'},
                        {loader: 'less-loader'},
                    ]
                },
                {
                    // 详细loader配置
                    test: /\.html$/,
                    use: [
                        {
                            loader: 'html-loader',
                            options: {
                                esModule: false
                            }
                        },
                    ]
                },
                {
                    // 详细loader配置
                    test: /\.(jpg|png|gif|svg)$/,
                    use: [
                        {
                            loader: 'url-loader',
                            options: {
                                /**
                                 * limit 参数说明：
                                 *  1. 图片小于8kb,就会被处理成base64
                                 *  2. 优点：减少请求数量（减轻服务器压力）
                                 *  3. 缺点：图片体积会更大（降低请求速度） 
                                 */
                                // limit: 8 * 1024,
                                esModule: false
                            },
                        }
                    ],
                    type: 'javascript/auto'
                },
                {
                    // 详细loader配置,另一种写法
                    test: /\.(jpg|png|gif|svg)$/,
                    loader: 'url-loader',
                    options: {
                        /**
                                 * limit 参数说明：
                                 *  1. 图片小于8kb,就会被处理成base64
                                 *  2. 优点：减少请求数量（减轻服务器压力）
                                 *  3. 缺点：图片体积会更大（降低请求速度） 
                                 */
                        // limit: 8 * 1024,
                        esModule: false
                    },
                    type: 'javascript/auto'
                },
    		],
    	},
    	// plugin的配置
    	plugins: [
            new HtmlWebpackPlugin({
                template: './static/index.html' 
            })
    	],
    	// 模式
    	mode: 'development'
    	// mode: 'production' 
    }
    ~~~

### 5.打包其他资源

> 不需要做任何处理，只需要原封不动的输出即可的资源，需要排除在打包处理的文件之外。如字体、icon图标等

* **安装插件**

  ~~~bash
  npm install file-loader
  ~~~

* **webpack.config.js**

  ~~~js
  /*
   webpack.config.js webpack配置文件
   作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
  
   所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
  */
  
  // resolve 用来拼接绝对路径方法
  const { resolve } = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  
  module.exports = {
  	// webpack 配置
  	// 入口起点
  	entry: './src/index.js',
  	// 输出路径
  	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
  	output: {
  		path: resolve(__dirname, 'dist')
  	},
  	// loader配置
  	module: {
  		rules: [
              {
                  test: /\.css$/,
                  use: [
                      {loader: 'style-loader'},
                      {loader: 'css-loader'}
                  ]
              },
              {
                  test: /\.less$/,
                  use: [
                      {loader: 'style-loader'},
                      {loader: 'css-loader'},
                      {loader: 'less-loader'},
                  ]
              },
              {
                  test: /\.html$/,
                  use: [
                      {
                          loader: 'html-loader',
                          options: {
                              esModule: false
                          }
                      },
                  ]
              },
              {
                  // 排除需要打包处理的资源
                  exclude: /\.(css|less|html|js)$/,
                  use: [
                      {
                          loader: 'file-loader',
                      },
                  ]
              },
  		],
  	},
  	// plugin的配置
  	plugins: [
          new HtmlWebpackPlugin({
              template: './static/index.html' 
          })
  	],
  	// 模式
  	mode: 'development'
  	// mode: 'production' 
  }
  ~~~

### 3. devServer（开发服务器）

> **devServer：**开发服务器，用来自动化（编译、打开浏览器、刷新浏览器）
>
> **特点：**只会在内存中编译打包，不会有任何输出
>
> **启动指令：** npm webpack server

* **安装插件**

  ~~~js
  npm install webpack-dev-server
  ~~~

  

* **webpack.config.js**

~~~js
/*
 webpack.config.js webpack配置文件
 作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）

 所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
*/

// resolve 用来拼接绝对路径方法
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	// webpack 配置
	// 入口起点
	entry: './src/index.js',
	// 输出路径
	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
	output: {
        filename: 'js/built.js',
		path: resolve(__dirname, 'dist')
	},
	// loader配置
	module: {
		rules: [
            {
                // 详细loader配置
                test: /\.css$/,
                use: [
                    // use数组中的loader执行顺序：从右到左，从下到上
                    // 创建style标签，将js中得到样式资源插入，添加到header中生效
                    {loader: 'style-loader'},
                    // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                    {loader: 'css-loader'}
                ]
            },
            {
                // 详细loader配置
                test: /\.less$/,
                use: [
                    // use数组中的loader执行顺序：从右到左，从下到上
                    // 创建style标签，将js中得到样式资源插入，添加到header中生效
                    {loader: 'style-loader'},
                    // 将css文件变成commanjs模块加载到js中，里面内容是样式字符串
                    {loader: 'css-loader'},
                    // 将less文件编译成css文件
                    {
                        loader: 'less-loader',
                },
                ]
            },
            // {
            //     // 详细loader配置
            //     test: /\.html$/,
            //     use: [
            //         {
            //             loader: 'html-loader',
            //             options: {
            //                 esModule: false
            //             }
            //         },
            //     ]
            // },
            {
                // 排除需要打包处理的资源后的其他文件，对其不做任何处理
                exclude: /\.(css|less|html|js|svg)$/,  // 排除这些文件
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            outputPath: 'md'
                        }
                    }
                ]
            },
            {
                // 详细loader配置
                test: /\.(jpg|png|gif|svg)$/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            /**
                             * limit 参数说明：
                             *  1. 图片小于8kb,就会被处理成base64
                             *  2. 优点：减少请求数量（减轻服务器压力）
                             *  3. 缺点：图片体积会更大（降低请求速度） 
                             */
                            // limit: 8 * 1024,
                            esModule: false,
                            outputPath: 'imgs'
                        },
                    }
                ],
                type: 'javascript/auto'
            },
		],
	},
	// plugin的配置
	plugins: [
        new HtmlWebpackPlugin({ 
            template: './static/index.html' 
        })
	],
	// 模式
	mode: 'development',
	// mode: 'production'

    devServer: {
        // 启动gzip压缩
        compress: true,
        // 启动端口
        port: 3000,
        // 自动打开浏览器
        open: true
    }
}
~~~



## 四：生产配置

### 1. css处理

#### （1）提取css

> css文件打包时间，默认会打入到js文件中，通过提取css文件，减小js文件大小，使文件更清晰

* **安装插件**

  ~~~bash
  npm install mini-css-extract-plugin
  ~~~

* **webpack.config.js**

  ~~~js
  // resolve 用来拼接绝对路径方法
  const { resolve } = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')
  
  module.exports = {
  	entry: './src/index.js',
  	output: {
          filename: 'js/built.js',
  		path: resolve(__dirname, 'dist')
  	},
  	// loader配置
  	module: {
  		rules: [
              {
                  // 详细loader配置
                  test: /\.css$/,
                  use: [MiniCssExtractPlugin.loader, 'css-loader']
              },
  		],
  	},
  	// plugin的配置
  	plugins: [
  
          new HtmlWebpackPlugin({ 
              template: './static/index.html' 
          }),
          new MiniCssExtractPlugin({
              filename: 'css/built.css'
          })
  	],
  	// 模式
  	// mode: 'development',
  	mode: 'production',
  
  
  }
  ~~~

#### （2）css兼容处理

#### （3）压缩CSS

* **安装插件**

  ~~~bash
  npm install optimize-css-assets-webpack-plugin
  ~~~

* **webpack.config.js**

  ~~~js
  /*
   webpack.config.js webpack配置文件
   作用：指示webpack干哪些活，（当运行webpack指令时，会加载里面的配置）
  
   所有构建工具都是基于nodejs平台运行的~模块化，默认采用commonjs
  */
  
  // resolve 用来拼接绝对路径方法
  const { resolve } = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')
  const OptimizeCssAssetsAebpackPlugin = require('optimize-css-assets-webpack-plugin')
  
  module.exports = {
  	// webpack 配置
  	// 入口起点
  	entry: './src/index.js',
  	// 输出路径
  	// __dirname nodejs的变量，代表当前问及那的目录绝对路径
  	output: {
          filename: 'js/built.js',
  		path: resolve(__dirname, 'dist')
  	},
  	// loader配置
  	module: {
  		rules: [
              {
                  // 详细loader配置
                  test: /\.css$/,
                  use: [MiniCssExtractPlugin.loader, 'css-loader']
              },
  		],
  	},
  	// plugin的配置
  	plugins: [
  
          new HtmlWebpackPlugin({ 
              template: './static/index.html' 
          }),
          new MiniCssExtractPlugin(),
          new OptimizeCssAssetsAebpackPlugin()
  	],
  	// 模式
  	// mode: 'development',
  	mode: 'production',
  }
  ~~~

  

* 



## 五：性能优化

### 1. 开发环境性能优化

* **优化代码构建速度**
* **优化代码调试**

### 2. 生产环境性能优化

* **优化打包构建速度**
* **优化代码运行的性能**

### 3.HMR

> **HMR： ** hot module replacement  热模块替换 / 模块热替换
>
> **作用：** 一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块），极大的提升构建速度

~~~js

~~~

