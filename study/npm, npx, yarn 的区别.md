### 1，yarn 和 npm 的区别

yarn 和 npm 都是 node 软件包管理器，

 yarn: 是同步执行所有任务，提高了性能， 而且如果你上一次安装过软件包，第二个会从缓存中获取，
npm: 是按照队列执行每一个package, 每一次都是从网络上下载，也就是说必须要等到当前 package 安装完成之后，才能继续后面的安装

"6.0.3",  // 表示安装指定的6.0.3版本
"~6.0.3", // 表示安装6.0.X中最新的版本
"^6.0.3"  // 表示安装6.X.X中最新的版本

### 2，yarn 和 npm 命令对比

 NPM                                                           Yarn
npm install                     			       ==     yarn
npm install vue -g              		      ==     yarn global add vue
npm install vue --save          		   ==     yarn add vue
npm install vue --save-dev      	   ==     yarn add vue --dev
npm uninstall vue --save(-dev)  	==     yarn remove vue
npm unpdate vue --save          	  ==     yarn upgrade vue 

### 3，npm 和 npx 的区别

* npm 是 node 软件包的管理器
* npx 是 node 软件包的执行工具
  官方文档，从npm@5.2.0 版本开始，npx就和npm捆绑在了一起，可以认为npx是npm 的高级版本，npx 具有更强大的功能，
  ** npx是一个可执行的二进制文件，原理很简单，运行npx的时候，默认会到 node_modules/.bin 路径和环境变量$PATH里面，检查命令是否存在

For Example:
项目中想运行一个脚本命令有两种方式：
 1， package.json中配置script脚本
 {
   "scripts": {
     "mocha": xxxx
   }
 }
 2, 项目根目录路径下面：
  node-modules/.bin/mocha --version

现在有了npx可以直接执行：
npx mocka --version

*** 执行一次性命令
npx 当执行一个包的时候，会自动检查本地是否存在，如果没有会为你从 npm 上下载，临时安装这个包，并且执行它。当做完这些事情后，已安装的包不会出现在你的全局安装中，所以不用担心长期使用所带来的全局污染。
常用的命令： npx create-react-app my-app
安装一个临时create-react-app并调用,而不用污染全局安装

总结：执行npx命令
1.首先会检查本地项目路径中是否存在要执行的包
2.如果存在，执行；
3.如果不存在，意味着尚未安装该软件包，npx将临时安装其最新版本，然后执行它；