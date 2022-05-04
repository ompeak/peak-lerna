介绍下我们需要使用到的**npm**模块

- `prettier` 代码格式化模块，和vscode上安装的Vetur扩展工具使用的格式化相同
- `husky` git的钩子，在git的hook中执行一些命令
- `lint-staged ` 对git暂存的文件进行lint检查
- `eslint`  js代码检测工具

##  第1步

安装所需依赖

```
npm install --save-dev prettier husky@4.3.8 lint-staged eslint
```

## 第2步

### 2.1 配置prettier的规则

在项目更目录新建一个 `.prettierrc` 文件，内容为（这里为了方便加了中文注释，实际需要将注释去掉）

```
{
    // 代码换行长度
    "printWidth": 200,
    // 代码缩进空格数
    "tabWidth": 2,
    // 使用制表符缩进而不是空格缩进
    "useTabs": true,
    // 代码结尾是否加分号
    "semi": false,
    // 是否使用单引号
    "singleQuote": true,
    // 对象大括号内两边是否加空格 { a:0 }
    "bracketSpacing": true,
    // 单个参数的箭头函数不加括号 x => x
    "arrowParens": "avoid"
}
```

更多配置可以查看prettier官网配置：[prettier.io/docs/en/opt…](https://prettier.io/docs/en/options.html)

###  2.2 配置eslint规则

因为没有全局安装eslint，所以在项目中执行(初始化eslint文件) ，根据eslint的提示选项，具体根据项目不同选择不同

```
./node_modules/.bin/eslint --init
```

在项目自动在根目录新增加了一个.eslintrc.json文件，在rules选项中校验规则：

```
"rules":{
    // 禁止对象字面量中出现重复的 key
    "no-dupe-keys": "error",
    // 禁止出现重复的 case 标签
    "no-duplicate-case": "error",
    // 禁止出现空语句块,允许catch出现空语句
    "no-empty": ["error", {"allowEmptyCatch": true}],
    // 禁止对 catch 子句的参数重新赋值
    "no-ex-assign":"error",
    // 禁止不必要的布尔转换
    "no-extra-boolean-cast": "error",
    // 禁止不必要的分号
    "no-extra-semi": "error",
    // 强制所有控制语句使用一致的括号风格
    "curly": "error"
    
}复制代码
```

更多校验规则可以查看eslint官网配置：[cn.eslint.org/docs/rules/](https://cn.eslint.org/docs/rules/)

## 第3步

在package.json中增加husky和lint-staged的配置

```
"husky":{
    "hooks": {
        "pre-commit": "lint-staged"
    }
},
"lint-staged": {
    "src/**": [
        "prettier --config .prettierrc --write",
        "eslint --fix",
        "git add"
    ]
}
```

具体检查路径在lint-staged 配置项可以配置，具体查看官网

**注：lint-staged配置项中一定要加"git add"，否则格式化修改后的文件，没有被commit到本地仓库中**

## 第4步

检验是否成功，修改一些文件后，执行

```
git add .
```

```
git commit -m '测试'
```

此时就会自动格式化后进行eslint校验，如果有错误的话，commit会失败，并给出eslint提示的错误，修改后再次提交就可以了、



需将检查图片忽略，否则检查不通过（创建以下文件并分别加入`/src/img`）

.eslintignore文件 忽略eslint检查的路径

.prettierignore文件 忽略prettier格式化的路径



