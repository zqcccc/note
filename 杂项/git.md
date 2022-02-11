## cherry-pick

`git cherry-pick <提交号>...`

如果你想将一些提交复制到当前所在的位置（`HEAD`）下面的话， Cherry-pick 是最直接的方式，cherry-pick 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。

## rebase

交互式 rebase 指的是使用带参数 `--interactive` 的 rebase 命令, 简写为 `-i`

如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

在实际使用时，所谓的 UI 窗口一般会在文本编辑器 —— 如 Vim —— 中打开一个文件。 考虑到课程的初衷，我弄了一个对话框来模拟这些操作。

当 rebase UI界面打开时, 你能做3件事:

- 调整提交记录的顺序（通过鼠标拖放来完成）
- 删除你不想要的提交（通过切换 `pick` 的状态来完成，关闭就意味着你不想要这个提交记录）
- 合并提交。 遗憾的是由于某种逻辑的原因，我们的课程不支持此功能，因此我不会详细介绍这个操作。简而言之，它允许你把多个提交记录合并成一个。

## ^~

操作符 `^` 与 `~` 符一样，后面也可以跟一个数字。

但是该操作符后面的数字与 `~` 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交。还记得前面提到过的一个合并提交有两个父提交吧，所以遇到这样的节点时该选择哪条路径就不是很清晰了。

Git 默认选择合并提交的“第一个”父提交，在操作符 `^` 后跟一个数字可以改变这一默认行为。

`git checkout HEAD~^2~2`

## rebase 和 merge 合并 commit

`git rebase <base> <需要被变基的分支名>` 变基，这样你会把分支去掉，合到新的 base 上，分支会更干净

git merge 生成创建新的合并提交，合入的分支的原来的分支并不受影响

在开发社区里，有许多关于 merge 与 rebase 的讨论。以下是关于 rebase 的优缺点：

优点:

- Rebase 使你的提交树变得很干净, 所有的提交都在一条线上

缺点:

- Rebase 修改了提交树的历史

比如, 提交 C1 可以被 rebase 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。

一些开发人员喜欢保留提交历史，因此更偏爱 merge。而其他人可能更喜欢干净的提交树，于是偏爱 rebase。仁者见仁，智者见智。 :D

## push & fetch & pull

git fetch 是更新了本地的 remote 分支，即叫 origin/** 的分支，remote 我们一般叫 origin

`git push <remote> <place>` place 就是分支名，一般我们本地和远程的分支名是一样的，这样我直接写分支名就能推到远程的相应的分支上

`git push origin <local source>:<remote branch>` 这里的 local source 是可以是一个 commit hash 或者 `HEAD^` `HEAD~3` 这样的 commit 标识

有 push 相应的 fetch 也有参数，不过和 push 相反， `git fetch origin foo` 就把远程的 origin/foo 拉到了本地的 origin/foo

如果是 git fetch origin foo:test 就是把远程的 origin/foo 拉到本地的 test 分支上，即便本地的 test 不存在也会在 fetch 前创建新分支

如果 `git fetch` 没有参数，它会下载所有的提交记录到各个远程分支

git push origin :foo 如果 push 空 到远程仓库它会删除远程仓库中的分支！

git fetch origin :foo 如果 fetch 空 到本地，会在本地创建一个新分支。

git pull 其实就是 git fetch 和 git merge 的结合

以下命令在 Git 中是等效的:

`git pull origin foo` 相当于：

```
git fetch origin foo; git merge o/foo
```

还有...

`git pull origin bar~1:bugFix` 相当于：

```
git fetch origin bar~1:bugFix; git merge bugFix
```

可以看到 git pull 实际上就是 fetch + merge 的缩写, git pull 唯一关注的是提交最终合并到哪里（也就是为 git fetch 所提供的 destination 参数）

