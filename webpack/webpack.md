#### 一：webpack基础概念

##### 1.什么是webpack

> 1. webpack是一种前端资源构建工具，一个静态模块打包器（module bundler）
> 2. 在webpack看来，前端的所有资源文件（js/json/less/img/css/pns...）都会作为模块处理
> 3. 他将根据模块的依赖关系进行静态分析，打包生成静态资源（bundle）

##### 2.webpack 五个核心概念

###### （1）Entry

入口（entry）：指示webpack以那个文件为入口起点开始打包，分析构建内部依赖图

###### （2）Output

输出（output）：指示webpack打包后的资源bundles输出到那里去，以及如何命名

###### （3）Loader

loader 让 webpack 能够去处理那些非JavaScript文件（webpack本身只理解JavaScript）

###### （4）Plugins

插件（plugins）可以用于执行范围更广的任务。插件的范围包括：从打包优化和压缩，一直到重新定义环境中的变量等

###### （5）Mode

模式（Mode）指示webpack 使用相应模式的配置

| 选项        | 描述                                                         | 特点                           |
| ----------- | ------------------------------------------------------------ | ------------------------------ |
| development | 会将`process.env.NODE_ENV`的值设置为`development`<br />启用`NamedChunksPlungin` 和 `NamedModulesPlugin` | 能让代码本地调试运行运行的环境 |
| production  | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `production`。<br />为模块和 chunk 启用确定性的混淆名称，`FlagDependencyUsagePlugin`，<br />`FlagIncludedChunksPlugin`，`ModuleConcatenationPlugin`，<br />`NoEmitOnErrorsPlugin` 和 `TerserPlugin` | 能让代码优化上线环境           |
| none        | 不适用任何默认优化选项                                       |                                |

如果没有设置，webpack会给`mode`的默认值设置为`production`







