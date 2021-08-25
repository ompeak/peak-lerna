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

![](/images/1.png)

#### lerna 常用命令

介绍一些 `lerna` 常用的命令,常用命令这部分可以简单过一遍，当作一个工具集收藏就行，需要的时候来找下，用着用着就熟练了，主要可以实操下下面的实战小练习，这个过程会遇到一些坑的

初始化 `lerna` 项目

```
lerna init
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

增加模块包到 `packages` 中指定项目 下面是将 `npm-modele1` 模块增加到 `components1` 项目中

```go
lerna add modele1 --scope=components1
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

在当前项目中发布包

```go
lerna publish
```

这个命令可以结合 `lerna.json` 中的  `"version": "independent"` 配置一起使用，可以完成统一发布版本号和`packages` 下每个模版发布的效果，具体会在下面的实战讲解。

> `lerna publish` 永远不会发布标记为 `private` 的包（`package.json中的”private“: true`）

以上命令基本够日常开发使用了，如果需要更详细内命令内容，可以查看下面的详细文档 lerna 命令详细文档





