1. 初始化一个 node 项目：`npm init -y`

2. 安装所需依赖。`npm install --save-dev @commitlint/config-conventional @commitlint/cli husky@4.3.8`

3. 在工程根目录下新建配置文件，名称为 `commitlint.config.js`。

4. 在 commitlint.config.js 中添加配置信息

   ```
   const types = [
     'build', 
     'ci', 
     'chore',
     'docs', 
     'feat', 
     'fix', 
     'pref', 
     'refactor', 
     'revert', 
     'style', 
     'test'
   ];
   
   typeEnum = {
     rules: {
       'type-enum': [2, 'always', types]
     },
     value: () => types
   }
   
   module.exports = {
       extends: [
         "@commitlint/config-conventional"
       ],
       rules: {
         'type-case': [0],
         'type-empty': [0],
         'scope-empty': [0],
         'scope-case': [0],
         'subject-full-stop': [0, 'never'],
         'subject-case': [0, 'never'],
         'header-max-length': [0, 'always', 72],
         'type-enum': typeEnum.rules['type-enum']
       }
     };
   ```

5. 在 package.json 文件中添加以下代码，代码层级跟 devDependencies 同级。

   ```
   "husky": {
       "hooks": {
           "pre-commit": "echo '哈喽，小伙伴们，在这里可以做测试相关的逻辑哦，一般结合公司的 ci'",
           "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
           "pre-push": "echo 提交代码前需要先进行单元测试 && 可以做测试相关"
       }
   }
   ```

上面的流程配置完成，当你在提交 commit 信息的输入的内容，如果不符合 `<type>: <subject>` 规则，会终止并给出提示信息。

type 就是上面的种类；subject 就是需要提交的文字概括。比如：feature：增加********功能。

小说明：如果某次提交想禁用 husky，可以添加参数 **--no-verify**。`git commit --no-verify -m "xxx"`

