---
typora-root-url: ../public
---

### lerna是什么

#### lerna基本概念

> A tool for managing JavaScript projects with multiple packages. Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm.

翻译：`Lerna`是一个用来优化托管在 `git\npm` 上的多 `package` 代码库的工作流的一个管理工具,可以让你在主项目下管理多个子项目，从而解决了多个包互相依赖，且发布时需要手动维护多个包的问题。

关键词：多仓库管理，多包管理，自动管理包依赖

#### lerna 解决了哪些痛点

资源浪费

通常情况下，一个公司的业务项目只有一个主干，多 `git repo` 的方式，这样 `node_module` 会出现大量的冗余，比如它们可能都会安装 `React`、`React-dom` 等包，浪费了大量存储空间。

调试繁琐

很多公共的包通过 `npm` 安装，想要调试依赖的包时，需要通过 `npm link` 的方式进行调试。

资源包升级问题

一个项目依赖了多个 `npm` 包，当某一个子 `npm` 包代码修改升级时，都要对主干项目包进行升级修改。(这个问题感觉是最烦的，可能一个版本号就要去更新一下代码并发布)

### lerna基本使用

#### lerna 环境配置

`lerna` 在使用之前需要全局安装 `lerna` 工具。

```
npm install -g lerna
```

初始化一个lerna 项目

`mkdir lerna-demo`,在当前目录下创建文件夹`lerna-demo`,然后使用命令 `lerna init`执行成功后，目录下将会生成这样的目录结构。，一个 `hello world`级别的 `lerna` 项目就完成了。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8o20ot8j607705tjrb02.jpg)

#### lerna 常用命令

介绍一些 `lerna` 常用的命令,常用命令这部分可以简单过一遍，当作一个工具集收藏就行，需要的时候来找下，用着用着就熟练了，主要可以实操下下面的实战小练习，这个过程会遇到一些坑的

初始化 `lerna` 项目

```
lerna init # 固定模式(Fixed mode)默认为固定模式，packages下的所有包共用一个版本号(version)
lerna init --independent # 独立模式(Independent mode)，每一个包有一个独立的版本号
```

创建一个新的由 `lerna` 管理的包。

```go
lerna create <name>
```

安装所有·依赖项并连接所有的交叉依赖

```go
lerna bootstrap
```

增加模块包到最外层的公共 `node_modules`中

```go
lerna add axios
```

为packages文件夹下的package安装依赖

```go
lerna add <package>[@version] [--dev] # 命令签名

# 例如
lerna add module-1 --scope=module-2 # 将 module-1 安装到 module-2
lerna add module-1 --scope=module-2 --dev # 将 module-1 安装到 module-2 的 devDependencies 下
lerna add module-1 # 将 module-1 安装到除 module-1 以外的所有模块
lerna add babel-core # 将 babel-core 安装到所有模块
```

卸载依赖

```
$ lerna exec -- <command> [..args] # 在所有包中运行该命令

# 例如
$ lerna exec --scope=npm-list  yarn remove listr # 将 npm-list 包下的 listr 卸载
$ lerna exec -- yarn remove listr # 将所有包下的 listr 卸载
```

在 `packages` 中对应包下的执行任意命令 下面的命令，是对 `packages` 下的 `example-web` 项目执行 `yarn start` 命令 ，比较常用，可以把它配置到最外层的 `package.json` 中。

```go
lerna exec --scope example-web -- yarn start
```

如果命令中不增加 `--scope example-web`直接使用下面的命令，这会在 `packages` 下所有包执行命令`rm -rf ./node_modules`

```go
lerna exec -- rm -rf ./node_modules
```

显示所有的安装的包

```go
lerna list // 等同于 lerna ls
```

这里再提一个命令也比较常用,可以通过`json`的方式查看 `lerna` 安装了哪些包,`json` 中还包括包的**路径**，有时候可以用于查找包是否生效。

```go
lerna list --json
```

从所有包中删除 `node_modules` 目录

```go
lerna clean
```

> ⚠️注意下 `lerna clean` 不会删除项目最外层的根 `node_modules`

lerna run

运行npm script，可以指定具体的package。

```
lerna run <script> -- [..args] # 在所有包下运行指定

# 例如
lerna run test # 运行所有包的 test 命令
lerna run build # 运行所有包的 build 命令
lerna run --parallel watch # 观看所有包并在更改时发报，流式处理前缀输出
lerna run --scope my-component test # 运行 my-component 模块下的 test
```

在当前项目中发布包

```go
lerna publish
```

这个命令可以结合 `lerna.json` 中的  `"version": "independent"` 配置一起使用，可以完成统一发布版本号和`packages` 下每个模版发布的效果，具体会在下面的实战讲解。

> `lerna publish` 永远不会发布标记为 `private` 的包（`package.json中的”private“: true`）

以上命令基本够日常开发使用了，如果需要更详细内命令内容，可以查看下面的详细文档 lerna 命令详细文档

#### lerna.json解析

```
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": [
        "ignored-file",
        "*.md"
      ]
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]      
    }
  },
  "packages": ["packages/*"]
}
```

**version：**当前库的版本
**npmClient：** 允许指定命令使用的client， 默认是 npm， 可以设置成 yarn
**command.publish.ignoreChanges：**可以指定那些目录或者文件的变更不会被publish
**command.bootstrap.ignore：**指定不受 bootstrap 命令影响的包
**command.bootstrap.npmClientArgs：**指定默认传给 lerna bootstrap 命令的参数
**command.bootstrap.scope：**指定那些包会受 lerna bootstrap 命令影响
**packages：**指定包所在的目录

### 使用lerna提升开发流程体验

接下来，我们从一个demo出发，了解基于lerna的开发流程。

#### 项目初始化

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8ll95xij60k00b974e02.jpg)

我们需要维护一个UI组件库，其包含2个组件，分别为House（房子）和Window（窗户）组件，其中House组件依赖于Window组件。
我们使用lerna初始化整个项目，并且在packages里新建了2个package，执行命令进行初始化：

```
lerna init
```

初始化化后目录结构如下所示：

> ```
> .
> ├── lerna.json
> ├── package.json
> └── packages
>     ├── house
>     │   ├── index.js
>     │   ├── node_modules
>     │   └── package.json
>     └── window
>         ├── index.js
>         ├── node_modules
>         └── package.json
> ```

### 增加依赖

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8m8td4ij60k00b9glr02.jpg)

接下来，我们来为组件增加些依赖，首先House组件不能只由Window构成，还需要添加一些外部依赖（在这里我们假定为lodash）。我们执行：

```
lerna add lodash --scope=house
```

这句话会将lodash增添到House的dependencies属性里，这会儿你可以去看看package.json是不是发生变更了。

我们还需要将Window添加到House的依赖里，执行：

```
lerna add window --scope=house
```

就是这么简单，它会自动检测到window隶属于当前项目，直接采用symlink的方式关联过去。

symlink:符号链接，也就是平常所说的建立超链接，此时House的node_modules里的Window直接链接至项目里的Window组件，而不会再重新拉取一份，这个对本地开发是非常有用的。

#### 发布到npm

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8mrwuz2j60k00b9mxe02.jpg)



接下来我们只需要简单地执行lerna publish，确认升级的版本号，就可以批量将所有的package发布到远程。

> 默认情况下会推送到系统目前npm对应的registry里，实际项目里可以根据配置leran.json切换所使用的npm客户端。

### 更新模块

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8n3ojfyj60k00b93yr02.jpg)





接下来，我们变更了Window组件，执行一下lerna updated，便可以得知有哪些组件发生了变更。

```
→ lerna updated
lerna info version 2.9.1
lerna info Checking for updated packages...
lerna info Comparing with v1.0.9.
lerna info Checking for prereleased packages...
lerna info result
```

我们可以看到，虽然我们只变更了window组件，但是lerna能够帮助我们检查到所有依赖于它的组件，对于没有关联的组件，是不会出现在更新列表里的，这个对于相比之前人工维护版本依赖的更新，是非常稳健的。

#### 集中版本号或独立版本号

截止目前，我们已经成功发布了2个package，现在再新增一个Tree组件，它和其他2个package保持独立，随后我们执行lerna publish，它会提示Tree组件的版本号将会从0.0.0升级至1.0.0，但是事实上Tree组件仅仅是刚创建的，这点不利于版本号的语义化，lerna已经考虑到了这一点，它包含2种版本号管理机制。

- fixed模式下，模块发布新版本时，都会升级到leran.json里编写的version字段
- independent模式下，模块发布新版本时，会逐个询问需要升级的版本号，基准版本为它自身的package.json，这样就避免了上述问题。

如果需要各个组件维护自身的版本号，那么就使用independent模式，只需要去配置leran.json即可。

#### 总结

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtt8nev8mmj60k00b93yr02.jpg)



lerna不负责构建，测试等任务，它提出了一种集中管理package的目录模式，提供了一套自动化管理程序，让开发者不必再深耕到具体的组件里维护内容，在项目根目录就可以全局掌控，基于npm scripts，可以很好地完成组件构建，代码格式化等操作，并在最后一公里，用lerna变更package版本，将其上传至远端。