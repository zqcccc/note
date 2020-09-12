# Git Hooks 检查

Git 基本已经成为项目开发中默认的版本管理软件，在使用 Git 的项目中，我们可以为项目设置 Git Hooks 来帮我们在提交代码的各个阶段做一些代码检查等工作。在这里，我们来了解一下如何将 ESLint 与 Git Hooks 结合起来。

## Git Hooks 介绍

钩子（Hooks） 都被存储在 Git 目录下的 hooks 子目录中。 也就是绝大部分项目中的 `.git/hook` 目录。钩子分为两大类，客户端的和服务器端的。客户端钩子主要被提交和合并这样的操作所调用，而服务器端钩子作用于接收被推送的提交这样的联网操作，这里我们主要介绍客户端钩子。详细资料可以参看[官方文档](https://git-scm.com/book/zh/v2/自定义-Git-Git-钩子)。

### 常见的客户端钩子

### pre-commit

`pre-commit` 钩子在输入提交信息前运行。它用于检查即将提交的快照，例如，检查是否有所遗漏，确保测试运行，以及核查代码。 如果该钩子以非零值退出，Git 将放弃此次提交，可以用 `git commit --no-verify` 来绕过这个环节。 我们可以利用 `pre-commit` 来检查代码风格是否一致（运行类似 lint 的程序）、尾随空白字符是否存在（自带的钩子就是这么做的），或新方法的文档是否适当等等。

### pre-push

钩子会在 git push 运行期间， 更新了远程引用但尚未传送对象时被调用。 它接受远程分支的名字和位置作为参数，同时从标准输入中读取一系列待更新的引用。 你可以在推送开始之前，用它验证对引用的更新操作（一个非零的退出码将终止推送过程）。

`pre-commit` 和 `pre-push` 是我们在项目中比较常用的钩子，我们可以在这两个阶段来为我们的代码做 Lint 检查。

## ESLint 与 Git Hooks

### pre-push 配置

```bash
# .git/hooks/pre-push

#
# ESLint 检查
#

echo "开始 ESLint 检查"
# 运行 eslint 命令
npm run eslint
# $? 表示上一条命令的返回值，为 0 则成功，否则失败
if [ $? != $zero ]
then
    echo "ESLint 检查未通过"
    exit 1
fi
echo "ESLint 检查通过"
```

### package.json scripts 配置

```json
// package.json
"scripts":{
    "eslint": "eslint --config .eslintrc.prod.js --ext .js,.jsx --cache src",
}
```