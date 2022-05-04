### 插件如何工作

插件是基于Web技术构建的，例如HTML、JavaScript和CSS。它们在单独的沙盒执行环境中运行并与Chrome浏览器进行交互。

　　插件允许我们通过使用API修改浏览器行为和访问Web内容来扩展和增强浏览器的功能。插件通过最终用户UI和开发人员API进行操作：

- 扩展用户界面，这为用户提供了一种一致的方式来管理他们的扩展。
- 扩展的API允许浏览器本身的扩展的代码访问功能：激活标签，修改网络请求等等。

　　要创建插件程序，我们需要组合构成插件程序的一些资源清单，例如JS文件和HTML文件、图像等。对于开发和测试，可以使用扩展开发者模式将这些“解压”加载到Chrome中。如果我们对自己开发出来的插件程序感到满意，就可以通过网上商店将其打包并分享给其他的用户。

### 插件的原则

我们编写的插件想要发布到`Chrome网上应用店`中，就必须遵守网上应用店政策，它规定了以下几点：

- 插件必须满足一个定义狭窄且易于理解的单一目的。单个插件可以包含多个组件和一系列功能，只要一切都有助于实现一个共同的目的。
- 用户界面应该是最小的并且有意图。它们的范围可以从简单的图标到打开一个带有表单的新窗口。

### 插件结构

**Manifest**

用来配置插件的配置项，是插件开发必不可少的，必须放在更目录下面，

它有三个必填字段：manifest_version, name, version

```
manifest_version： #使用的manifest版本，目前最新版本3
icons: # 插件的图标资源
background: {
   service_worker: #项目的入口文件，在此处注册事件
}
action: {
  default_popup:# 弹窗页
}
options_page: #配置管理页
permissions: # 脚本允许使用的API资源列表
content_scripts: # 扩展程序向页面注入的脚本资源
web_accessible_resources: #普通页面能够直接访问的插件资源列表
default_locale: #默认语言
```

**Background Page**

是一个在后头持续运行的常驻页面，随着扩展加载而运行，知道浏览器关闭而停止，它负责维护扩展程序的全局状态，还可以和其他扩展通信，background的权限非常高，可以调用所有Chrome扩展API

**Popup**

点击插件时的弹窗，提供了基本的交互功能，有些插件没有弹窗

**Content Scripts**

内容脚本作用域web页面上下文，通过DOM操作来获取页面信息，操作页面节点，并能够将信息传递回扩展程序，可以注入到页面内，但是与页面的JS隔离，可以提前配置脚本的执行时机，默认：`run_at: document_idle` , 表示页面空闲时期

**Option**

插件的后台管理页面，右键扩展程序图标，点击菜单里面的选项，即可打开选项页面

###  实战

#### page-hello

目录

```
--page-hello
  --popup.html // 插件界面
  --icon.png // 插件图标
  --manifest.json // 配置文件
```

manifest.json

```
{
  "name": "Page Hello",
  "description": "Page Hello Description",
  "version": "1.0", 
  "manifest_version": 3,
  "action": {
    "default_popup": "popup.html", 
    "default_icon": "icon.png",
    "default_title": "Press MacCtrl+Shift+F"
  },
  "commands": {
    "_execute_action": {
      "suggested_key": { 
        "default": "Ctrl+Shift+F",
        "mac": "MacCtrl+Shift+F"
      },
      "description": "Opens hello.html"
    }
  }
}

```

popup.html

```
<!DOCTYPE html>
<html>
<head>
  <title>Hello</title>
</head>
<body>
  <h1>Hello</h1>
</body>
</html>
```

#### page-override

目录

```
--page-override
  --popup.html // 插件界面
  --override_page.html //覆盖页面
  --icon.png // 插件图标
  --manifest.json // 配置文件
```

manifest.json

```
{
  "name": "Page Override",
  "description": "Page Override Desription",
  "version": "1.0", 
  "manifest_version": 3,
  "action": {
    "default_popup": "popup.html", 
    "default_icon": "icon.png",
    "default_title": "Press MacCtrl+Shift+F"
  },
  "commands": {
    "_execute_action": {
      "suggested_key": { 
        "default": "Ctrl+Shift+F",
        "mac": "MacCtrl+Shift+F"
      },
      "description": "Opens hello.html"
    }
  },
  "chrome_url_overrides" : {
    "newtab": "override_page.html"
  }
}
```

popup.html

```
<!DOCTYPE html>
<html>
<head>
  <title>Hello</title>
</head>
<body>
  <h1>Hello</h1>
</body>
</html>
```

override_page.html

```
<!DOCTYPE html>
<html>
<head>
  <title>override page</title>
</head>
<body>
  <h1>override_page</h1>
</body>
</html>
```

#### page-redder

目录

```
--page-redder
  --background.js // 后台运行的脚本
  --manifest.json // 配置文件
```

manifest.json

```
{
  "name": "Page Redder",
  "description": "Turns the page red when you click the icon",
  "manifest_version": 3,
  "version": "1.0.0",
  "action": {},
  "permissions": [
    "activeTab",
    "scripting"
  ],
  "background": {
    "service_worker": "background.js"
  }
}
```

background.js

```
function reddenPage() {
  document.body.style.backgroundColor = 'red';
}

chrome.action.onClicked.addListener((tab) => {
  if(!tab.url.includes("chrome://")) {
    chrome.scripting.executeScript({
      target: { tabId: tab.id },
      function: reddenPage
    });
  }
});
```

####  page-redder-b

目录

```
--page-redder
  --background.js // 后台运行的脚本
  --content-script.js // 
  --manifest.json // 配置文件
```

manifest.json

```
{
  "name": "Page Redder B",
  "description": "Turns the page red when you click the icon",
  "manifest_version": 3,
  "version": "1.0.0",
  "action": {},
  "permissions": [
    "activeTab",
    "scripting"
  ],
  "background": {
    "service_worker": "background.js"
  }
}
```

background.js

```
chrome.action.onClicked.addListener((tab) => {
  chrome.scripting.executeScript({
    target: { tabId: tab.id },
    files: ['content-script.js']
  });
}); 
```

content-script.js

```
document.body.style.backgroundColor = 'orange';
```

#### page-bookmarks

```
├── icon.png
├── js
│   └── jquery-3.6.0.min.js
├── manifest.json
├── popup.html
└── popup.js
```

manifest.json

```
{
  "name": "Page Bookmarks",
  "description": "Page Bookmarks Description",
  "version": "1.0.0", 
  "manifest_version": 3,
  "permissions": [
    "bookmarks"
  ],
  "action": {
    "default_popup": "popup.html", 
    "default_icon": "icon.png"
  }
}
```

popup.html

```
<html>

<head>
</head>

<body style="width: 400px">
  <div>Search: <input id="search"></div>
  <div id="bookmarks"></div>
  <script src="./js/jquery-3.6.0.min.js"></script>
  <script src="popup.js"></script>
</body>

</html>
```

popup.js

```
document.addEventListener('DOMContentLoaded', function () {
    dumpBookmarks();
});

$('#search').change(function () {
    $('#bookmarks').empty();
    dumpBookmarks($('#search').val());
});

function dumpBookmarks(query) {
    var bookmarkTreeNodes = chrome.bookmarks.getTree(function (bookmarkTreeNodes) {
        $('#bookmarks').append(dumpTreeNodes(bookmarkTreeNodes, query));
    });
}

function dumpTreeNodes(bookmarkNodes, query) {
    var list = $('<ul>');
    for (var i = 0; i < bookmarkNodes.length; i++) {
        list.append(dumpNode(bookmarkNodes[i], query));
    }
    return list;
}

function dumpNode(bookmarkNode, query) {
    if (bookmarkNode.title) {
        if (query && !bookmarkNode.children) {
            if (String(bookmarkNode.title.toLowerCase()).indexOf(query.toLowerCase()) == -1) {
                return $('<span></span>');
            }
        }

        var anchor = $('<a>');
        anchor.attr('href', bookmarkNode.url);
        anchor.text(bookmarkNode.title);
        anchor.click(function () {
            chrome.tabs.create({ url: bookmarkNode.url });
        });

        var span = $('<span>');
        span.append(anchor);
    }

    var li = $(bookmarkNode.title ? '<li>' : '<div>').append(span);
    if (bookmarkNode.children && bookmarkNode.children.length > 0) {
        li.append(dumpTreeNodes(bookmarkNode.children, query));
    }

    return li;
}
```

