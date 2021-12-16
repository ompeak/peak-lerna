## 1. [react-select](https://link.juejin.cn?target=https%3A%2F%2Freact-select.com%2Fhome)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f7bb71bfdaaf4a44975443f42097821e~tplv-k3u1fbpfcp-watermark.awebp)

一个厉害的，强大的表单下拉选择框的库

代表了一种开发功能强大的 **react.js** 组件的全新方式，这些组件在完全可定制的同时开箱即用。

**突出的功能特性**

- 灵活的数据处理方法，具有可定制的功能。
- 灵活结合 **emotion** 这个库**(一个 css in js 的强大的库** ).
- 组件注入API，用于完全控制UI行为。
- 选项组、portal 支持、动画等。

安装:

**npm i react-select**

示例代码：

```js
import React, { Component } from 'react'
import Select from 'react-select'

const options = [
  { value: 'chocolate', label: 'Chocolate' },
  { value: 'strawberry', label: 'Strawberry' },
  { value: 'vanilla', label: 'Vanilla' }
]

const MyComponent = () => (
  <Select options={options} />
)
复制代码
```

## 2. [react-dnd](https://link.juejin.cn?target=https%3A%2F%2Freact-dnd.github.io%2Freact-dnd%2Fabout)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3851de2618cc4788b3629adc00254ca9~tplv-k3u1fbpfcp-watermark.awebp)

React 的拖拽包

一个强大的工具包，能够做出丰富的拖拽页面应用，而且代码具有解耦性。

**突出的功能**

- 非常适合**Trello（一个管理任务的工具）\**和\**Storify** 等应用程序，其中拖动负责在应用程序的不同部分之间传输数据。
- 建立在HTML5拖放API之上。

安装:

**npm i react-dnd**

示例代码：

```js
import React from 'react'
import { useDrag } from 'react-dnd'
import { ItemTypes } from './Constants'

/**
 * Your Component
 */
export default function Card({ isDragging, text }) {
  const [{ opacity }, dragRef] = useDrag({
    item: { type: ItemTypes.CARD, text },
    collect: (monitor) => ({
      opacity: monitor.isDragging() ? 0.5 : 1
    })
  })
  return (
    <div ref={dragRef} style={{ opacity }}>
      {text}
    </div>
  )
}
复制代码
```

## 3. [react-content-loader](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdanilowoz%2Freact-content-loader)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec08841d3ffd4111b6d71d5e2cad3810~tplv-k3u1fbpfcp-watermark.awebp)

基于 SVG 的强大的组件库，可以轻易地创造骨架式的 加载页面（loading）（有点像 Facebook 的卡片加载)

**突出的功能**

- **很多插件:** 有许多预设可供使用。
- **DIY:** 您可以使用create-content-loader轻松创建自己的加载程序。
- **React Native 支持:** 具有相同强大功能的相同API。
- **轻易:** 包小于 **2KB** and 零依赖

安装:

**npm i react-content-loader**

示例代码：

```js
import React from "react"
import ContentLoader from "react-content-loader"

const MyLoader = (props) => (
  <ContentLoader 
    speed={2}
    width={400}
    height={160}
    viewBox="0 0 400 160"
    backgroundColor="#f3f3f3"
    foregroundColor="#ecebeb"
    {...props}
  >
    <rect x="48" y="8" rx="3" ry="3" width="88" height="6" /> 
    <rect x="48" y="26" rx="3" ry="3" width="52" height="6" /> 
    <rect x="0" y="56" rx="3" ry="3" width="410" height="6" /> 
    <rect x="0" y="72" rx="3" ry="3" width="380" height="6" /> 
    <rect x="0" y="88" rx="3" ry="3" width="178" height="6" /> 
    <circle cx="20" cy="20" r="20" />
  </ContentLoader>
)

export default MyLoader
复制代码
```

## 4. [antd](https://link.juejin.cn?target=https%3A%2F%2Fant.design%2Findex-cn)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2f44440c6a74d998bd729433fc1ec04~tplv-k3u1fbpfcp-watermark.awebp)

企业级用户界面设计语言和**React UI**库。

突出的功能

- 使用 TypeScript 编写
- 一整套设计资源和开发工具。
- 每个细节都有强大的主题定制。

安装:

**npm i antd**

示例代码：

```js
import { useRequest } from 'umi';
import { queryProductList } from '@/services/product';

export default function useProductList(params: { pageSize: number; current: number }) {
  const msg = useRequest(() => queryUserList(params));

  const deleteProducts = async (id: string) => {
    try {
      await removeProducts(id);
      message.success('success');
      msg.run();
    } catch (error) {
      message.error('fail');
    }
  };

  return {
    dataSource: msg.data,
    reload: msg.run,
    loading: msg.loading,
    deleteProducts,
  };
}
复制代码
```

## 5. [gatsby-image](https://link.juejin.cn?target=https%3A%2F%2Fwww.gatsbyjs.com%2Fplugins%2Fgatsby-image%2F)

使用 **React**构建快速、现代的应用程序和网站

**突出的功能**

- **以极低代价托管:** Gatsby站点不需要服务器，因此您可以以服务器呈现站点的一小部分成本在CDN上托管整个站点。
- **从任何地方定位数据:** 从任何数据源 (Markdown文件，像Contentful或WordPress和REST API这样的无头CMS) 中提取数据。
- **超越静态站点:** 无任何限制的静态网站的好处。

安装:

**npm i gatsby-image**

示例代码：

```js
import React from "react"
import { graphql } from "gatsby"
import Img from "gatsby-image"

export default ({ data }) => (
  <div>
    <h1>Hello gatsby-image</h1>
    <Img fixed={data.file.childImageSharp.fixed} />
  </div>
)

export const query = graphql`
  query {
    file(relativePath: { eq: "blog/avatars/kyle-mathews.jpeg" }) {
      childImageSharp {
        # Specify the image processing specifications right in the query.
        # Makes it trivial to update as your page's design changes.
        fixed(width: 125, height: 125) {
          ...GatsbyImageSharpFixed
        }
      }
    }
  }
`
复制代码
```

## 6. [react-helmet](https://link.juejin.cn?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Freact-helmet)

可重用的 React 组件将管理您对文档头的所有更改。

采用纯HTML标签并输出纯HTML标签，非常简单，对 React 支持得很好。

**特性**

- 支持所有有效标签: **title, base, meta, link,** 等。
- 支持服务器端渲染。
- 嵌套组件覆盖重复的head更改。

安装:

**npm i react-helmet**

示例代码：

```js
import React from "react";
import {Helmet} from "react-helmet";
 
class Application extends React.Component {
  render () {
    return (
        <div className="application">
            <Helmet>
                <meta charSet="utf-8" />
                <title>My Title</title>
                <link rel="canonical" href="http://mysite.com/example" />
            </Helmet>
            ...
        </div>
    );
  }
};
复制代码
```

## 7. [react-virtualized](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fbvaughn%2Freact-virtualized)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/493bb827f9ce472bb78d413793ce6785~tplv-k3u1fbpfcp-watermark.awebp)

这提供了一个 React 组件来有效地呈现大列表和表格数据，由5个主要组件组成**(Grid, List, Table, Masonry, Collection)**

**突出的功能**

- 由于限制了要渲染的调用次数，因此提高了性能。
- 提供很多 HOC 组件，例如 **(AutoSizer, MultiGrid, etc)**

安装:

**npm i react-virtualized**

示例代码：

```js
import React from 'react';
import ReactDOM from 'react-dom';
import {Column, Table} from 'react-virtualized';
import 'react-virtualized/styles.css'; // only needs to be imported once

// Table data as an array of objects
const list = [
  {name: 'Brian Vaughn', description: 'Software engineer'},
  // And so on...
];

// Render your table
ReactDOM.render(
  <Table
    width={300}
    height={300}
    headerHeight={20}
    rowHeight={30}
    rowCount={list.length}
    rowGetter={({index}) => list[index]}>
    <Column label="Name" dataKey="name" width={100} />
    <Column width={200} label="Description" dataKey="description" />
  </Table>,
  document.getElementById('example'),
);
复制代码
```

## 8. [react-threesixty](https://link.juejin.cn?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Freact-threesixty)

使用 React 360有助于创造迷人的360虚拟现实体验，该体验延伸到台式机、手机和虚拟现实设备。

**突出的功能**

- 简化了复杂360和VR用户界面的创建。

安装:

**npm i react-threesixty**

示例代码：

```js
<ThreeSixtyViewer
  image: 'images/example.jpg'
  count: 19
  perRow: 4
/>
```

 