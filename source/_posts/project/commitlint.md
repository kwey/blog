---
title: git的commit规范及强制校验
tags: 'git'
categories: 'project'
top_img: '/img/git.png'
---
> commitlint 配合husky 在git push 代码之前检测commit messages

<a href="https://github.com/typicode/husky" target="_blank">https://github.com/typicode/husky</a>

<a href="https://commitlint.js.org/#/" target="_blank">https://commitlint.js.org/#/</a>

<a href="http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html" target="_blank">Commit message 和 Change log 编写指南</a>

## 一：安装commitlint
``` bash
npm install -D @commitlint/cli @commitlint/config-conventional
```
生成配置文件：> commitlint.config.js
``` javascript

module.exports = { 
 extends: ['@commitlint/config-conventional'],
 rules: { 
  'type-enum': 
    [2, 'always', 
      [ "feat", "fix", "docs", "style", "refactor", "test", "chore", "revert" ]
    ], 
   'subject-full-stop': [0, 'never'], 
   'subject-case': [0, 'never']
 }
};
```

二：安装husky
``` bash
npm install husky -D
```
husky继承了Git下所有的钩子，在触发钩子的时候，husky可以阻止不合法的commit,push等等。注意使用husky之前，必须先将代码放到git 仓库中，否则本地没有.git文件，就没有地方去继承钩子了。

项目下的package.json中配置：

``` javascript
"husky": {
    "hooks": {
        "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
}
  ```

三： 配合pretty-quick 格式化代码
``` bash
npm install --save-dev prettier pretty-quick
```



``` javascript
"husky": {
    "hooks": {
        "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
}
```




