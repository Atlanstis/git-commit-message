# git-commit-message
## 1. Commit Message 格式

目前规范使用较多的是 [Angular 团队的规范](https://link.juejin.im/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Fangular%2Fangular.js%2Fblob%2Fmaster%2FDEVELOPERS.md%23-git-commit-guidelines), 继而衍生了 [Conventional Commits specification](https://link.juejin.im/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fconventionalcommits.org%2F). 很多工具也是基于此规范, 它的 message 格式如下:

```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

其中，`Header` 是必填，`Body` 和 `Footer` 是选填。

### `Header`

`Header` 包括三个字段：`type`（必填）、`scope`（选填）和 `subject`（必填）

#### `type`

`type` 用于说明 `commit` 的类别，只允许使用下面 7 个标识。

- feat：新功能（feature）
- fix：修补bug
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改bug的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

`type` 为 `feat` 和 `fix`，则该 `commit` 将肯定出现在 `Change log` 之中。

#### `scope`

`scope` 用于说明 `commit` 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

#### `subject`

`subject` 是 `commit` 目的的简短描述，不超过50个字符

```
以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes
第一个字母小写
结尾不加句号（.）
```

### `body`

`Body` 部分是对本次 `commit` 的详细描述，可以分成多行。下面是一个范例。

```
More detailed explanatory text, if necessary.  Wrap it to 
about 72 characters or so. 

Further paragraphs come after blank lines.

- Bullet points are okay, too
- Use a hanging indent
```

### `Footer`

`Footer` 部分只用于两种情况

- 不兼容变动

如果当前代码与上一个版本不兼容，则 `Footer` 部分以 `BREAKING CHANGE` 开头，后面是对变动的描述、以及变动理由和迁移方法。

- 关闭 `Issue` 如果当前 `commit` 针对某个 `issue`，那么可以在 `Footer` 部分关闭这个 `issue`

```
Closes #123, #245, #992
```

## 2. `Commitizen`: 替代你的 git commit

项目级安装

```
npm install -D commitizen cz-conventional-changelog
```

在 `package.json`中配置：

```
"script": {
  ...,
  "commit": "git-cz",
},
"config": {
  "commitizen": {
    "path": "node_modules/cz-conventional-changelog"
  }
}
```

在对应项目中使用 `npm run commit` 即可。

![演示效果-1](https://github.com/Atlanstis/git-commit-message/blob/master/src/asset/show-1.gif)

## 3. `Commitlint`: 校验你的 message

[[commitlint](https://link.juejin.im?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Fmarionebl%2Fcommitlint): 可以帮助我们 lint commit messages, 如果我们提交的不符合指向的规范, 直接拒绝提交, 比较狠.

安装:

```
npm i -D @commitlint/config-conventional @commitlint/cli
```

同时需要在项目目录下创建配置文件 .commitlintrc.js, 写入:

```
module.exports = {
  extends: [
    "@commitlint/config-conventional"
  ],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'refactor', 'docs', 'chore', 'style', 'test'
     ]],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72]
  }
};
```

[rule配置说明](https://marionebl.github.io/commitlint/#/reference-rules):：rule由name和配置数组组成，如：`'name:[0, 'always', 72]'`，数组中第一位为level，可选`0,1,2`，0为disable，1为warning，2为error，第二位为应用与否，可选`always|never`，第三位该rule的值。

## 4. 结合 `Husky`

校验 commit message 的最佳方式是结合 git hook, 所以需要配合 [Husky](https://link.juejin.im/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Ftypicode%2Fhusky).

安装

```
npm install husky --save-dev
```

package.json 中添加:

```
"husky": {
  "hooks": {
    "commit-msg": "commitlint -e $GIT_PARAMS"
  }
},
```

