## 创建项目

进入你想创建项目的目录，运行 `yarn create vite` 然后根据提示输入项目名，然后依次选择 react、react-ts 即可创建一个 React+TS 项目。

创建后按照提示进入项目文件夹并安装依赖，然后运行 `yarn dev` 即可启动项目。不同于 Vite2，可以看到 Vite3 的默认端口号是 `5173`。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45a80abfcc3e42de9d1f1dab98b5d621~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

打开 [http://localhost:5173/](https://link.juejin.cn?target=http%3A%2F%2Flocalhost%3A5173%2F) 可以看到默认页面。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb13a347a1f14641b70868e7a30e17a8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 代码规范

### ESLint

我们通过下面的命令可以非常简单地进行 ESLint 的初始化。

```bash
npm init @eslint/config
复制代码
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b22cbd37988488991462d7434ef6135~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

按需选择完配置后，选择立即安装，就可一键安装相关依赖。安装成功后 ESLint 帮我们创建了 `.eslintrc.cjs` 配置文件（`cjs` 是指 CommonJS 格式）。

```js
module.exports = {
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint"
    ],
    "rules": {
    }
}
复制代码
```

可以看到生成的配置文件继承了 `"eslint:recommended", "plugin:react/recommended", "plugin:@typescript-eslint/recommended"` ，我们如果需要配置自己的 lint 规则可以在 rules 中添加。

### Prettier

一般 ESLint 用于检测代码风格代码规范，Prettier 用于对代码进行格式化。

先安装依赖

```shell
yarn add prettier -D
复制代码
```

然后再根目录创建 `.prettierrc.js` 配置文件

```js
module.exports = {
  printWidth: 80,
  tabWidth: 2,
  useTabs: false,
  singleQuote: true,
  semi: false,
  trailingComma: "none",
  bracketSpacing: true
}
复制代码
```

### ESLint + Prettier

接下来在 ESLint 中引入 Prettier，安装相关依赖。

```shell
yarn add eslint-config-prettier eslint-plugin-prettier -D
复制代码
```

现在更改 Eslint 的配置文件 `.eslintrc.cjs` 在里面加入 Prettier 相关配置。具体含义可见 [github.com/prettier/es…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fprettier%2Feslint-plugin-prettier%23recommended-configuration)

```diff
module.exports = {
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended",
        "plugin:@typescript-eslint/recommended",
+       "plugin:prettier/recommended"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint",
+       "prettier"
    ],
    "rules": {
+       "prettier/prettier": "error",
+       "arrow-body-style": "off",
+       "prefer-arrow-callback": "off"
    }
}
复制代码
```

接下来在 `package.json` 的 `script` 中添加命令。

```json
{
    "script": {
        "lint": "eslint --ext .js,.jsx,.ts,.tsx --fix --quiet ./"
    }
}
复制代码
```

尝试运行一下 `yarn lint` 但是发现报错了。

```javascript
Error [ERR_REQUIRE_ESM]: require() of ES Module /Users/Code/2022/leetcode-vj-fe/.prettierrc.js from /Users/Code/2022/leetcode-vj-fe/node_modules/prettier/third-party.js not supported.
复制代码
```

这个是 `.prettierrc.js` 中使用 CommonJS 语法报错，把 `.prettierrc.js` 也改名为 `.prettierrc.cjs` 就好了。

或者配置允许 CommonJS 语法，删除 `package.json` 中的 `"type": "module"`，同时修改 `.eslintrc.cjs` 的 `env`。

```diff
module.exports = {
  env: {
    browser: true,
    es2021: true,
+   node: true
  },
  // ...
}
复制代码
```

再试下发现还是有错误

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bdb267c1ee624b8e8a77fc4e459d52b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

错误看起来也比较明显，就是我们没有引入 React，在 React17 中，我们已经不需要为 JSX 显示引入 React 了，按照提示更改下 `.eslintrc.cjs`。

```js
module.exports = {
  extends: [
    // ...
    'plugin:react/jsx-runtime'
  ],
  //...
  settings: {
    react: {
      version: 'detect'
    }
  }
}
复制代码
```

再运行已经没有报错了，我们看到 ESLint 已经生效，对 App.tsx 进行了修复。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f76078bb9cc843c683d48e9a3a7b04c2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

### Vite 中引入 ESLint

在 Vite 中引入 ESLint 插件，以便在开发阶段发现问题。

```shell
yarn add vite-plugin-eslint -D
复制代码
```

然后在 `vite.config.ts` 引入插件

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import viteEslint from 'vite-plugin-eslint'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(),
    viteEslint({
      failOnError: false
    })
  ]
})
复制代码
```

我加了选项 `failOnError: false` 因为不想再开发阶段因为 ESLint 的错误打断开发，大家也可以使用默认配置，不传参数。现在我们可以在运行时的控制台看到 ESLint 的报错了。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3eeb62c1d1654078bbe84865250f5fec~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## Husky + lint-staged

### Husky

通过 Husky 在 Git commit 时进行代码校验。

首先安装依赖

```shell
yarn add husky -D
复制代码
```

然后在 package.json 中添加脚本 `prepare` 并运行

```shell
npm pkg set scripts.prepare="husky install"

npm run prepare
复制代码
```

运行命令后会在项目根目录创建 .husky 文件夹。

现在给 Husky 添加一个 Hook

```shell
npx husky add .husky/pre-commit "npm run lint"
复制代码
```

添加 hook 之后，每次 `git commit` 之前都会先运行 `npm run lint`，通过之后才会提交代码。

### lint-staged

每次提交都检测所有代码并不是一个好的决定，比如你只修改了文件 A 结果文件 B 报错了，但是文件 B 并不是你负责的模块，emmm改还是不改？

我们可以通过 lint-staged 只对暂存区的代码进行检验。

首先安装依赖

```shell
yarn add lint-staged -D
复制代码
```

然后在 `package.json` 添加相关配置。

```json
{
  "lint-staged": {
    "*.{js,jsx,tsx,ts}": [
      "npm run lint"
    ]
  }
}
复制代码
```

并在 `.husky/pre-commit` 中替换 `npm run lint` 为 `npx lint-staged`。现在我们每次提交代码前都会对改动的文件进行 Lint 检查。

## commitlint

使用 commitlint 对提交信息进行校验。先安装依赖：

```shell
yarn add @commitlint/cli @commitlint/config-conventional -D
复制代码
```

然后在根目录创建配置文件 `.commitlintrc.cjs`

```js
module.exports = {
  extends: ["@commitlint/config-conventional"]
}
复制代码
```

然后把 commitlint 命令也添加 Husky Hook。运行命令：

```sh
npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
复制代码
```

现在提交信息不合法就会被拦截导致提交失败，规范可见 [commitlint](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fcommitlint%23what-is-commitlint) ，你也可以根据需要修改提交信息规范。

现在我们已经配置好了一个 Vite+React+TS+ESLint+Prettier+Husky+Commitlint 项目，可以开始愉快地开发了。

 