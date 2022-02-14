常见的 `git add` `git commit` 就不说了，下面说一写重要甚至必要的命令

## cherry-pick

`git cherry-pick <提交号>...`

如果你想将一些提交复制到当前所在的位置（`HEAD`）下面的话， Cherry-pick 是最直接的方式，cherry-pick 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。

## rebase

交互式 rebase 指的是使用带参数 `--interactive` 的 rebase 命令, 简写为 `-i`

如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

在实际使用时，所谓的 UI 窗口一般会在文本编辑器 —— 如 Vim 中打开一个文件（VSCode 中的命令行是打开 Vim）。 

当 rebase UI界面打开时, 你能做3件事:

- 调整提交记录的顺序
- 删除你不想要的提交
- 合并提交。 简而言之，就是把多个提交记录 squash 合并成一个。

## ^~

例如命令 `git checkout HEAD~^2~2`

操作符 `^` 与 `~` 符一样，后面也可以跟一个数字。

但是该操作符后面的数字与 `~` 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交。还记得前面提到过的一个合并提交有两个父提交吧，所以遇到这样的节点时该选择哪条路径就不是很清晰了。

Git 默认选择合并提交的“第一个”父提交，在操作符 `^` 后跟一个数字可以改变这一默认行为。

## rebase 和 merge 合并 commit

`git rebase <base> <需要被变基的分支名>` 变基，合到新的 base 上，这个被变基的分支就是以新的 base 为基准的一个分支，这样就会走到 base 前面，两个分支就被合并了，分支的 commit 历史会更干净

`git merge` 生成创建新的合并提交，合入的分支的原来的分支并不受影响，只是当前分支

在开发社区里，有许多关于 merge 与 rebase 的讨论。以下是关于 rebase 的优缺点：

优点:

- Rebase 使你的提交树变得很干净, 所有的提交都在一条线上

缺点:

- Rebase 修改了提交树的历史

比如, 有顺序的历史 `commit C1 C2 C3`，提交 C1 可以被 `rebase` 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。

一些开发人员喜欢保留提交历史，因此更偏爱 merge。而其他人可能更喜欢干净的提交树，于是偏爱 rebase。仁者见仁，智者见智。 :D

## push & fetch & pull

### push

`git push` 不带任何参数是和 git 配置里的 `push.default` 有关系的，可以 `git config -l` 来查看

带参数的话一般是 `git push <remote> <place>` ，`<remote>` 我们一般叫 origin，`<place>` 就是分支名，一般我们本地和远程的分支名是一样的，这样我直接写分支名就能推到远程的相应的分支上

当然如果有本地分支名和远程分支名不一样的情况的话，可以用 `git push origin <local source>:<remote branch>` 这里的 `local source` 可以是一个 commit hash 或者 `HEAD^` `HEAD~3` 这样的 commit 标识

### fetch

有 `push` 把本地分支推到远程，那相应的就有 `fetch` 会把远程拉到本地，`git fetch` 是更新了本地的 `remote` 分支，即叫 `origin/**` 的分支，如果 `git fetch` 没有参数，它会下载所有的提交记录到各个远程分支

`git fetch <remote> <place>` 这里的 `<place>` 也可以是 `<source>:<destination>`

用 `git fetch origin foo` 就把远程的 `origin/foo` 拉到了本地的 `origin/foo`

`git fetch` 是更新了本地的 remote 分支，即叫 `origin/**` 的分支

如果是 `git fetch origin foo:test` 就是把远程的 `origin/foo` 拉到本地的 `test` 分支上，即便本地的 `test` 不存在也会在 `fetch` 前创建新分支

git push origin :foo 如果 push 空 到远程仓库它会删除远程仓库中的分支！

git fetch origin :foo 如果 fetch 空 到本地，会在本地创建一个新分支。

### merge

刚刚说了 `git fetch` 一般也就是更新本地的 `origin/**` 分支，但一般我不会去开发 `origin/**` 的分支，那这样还需要把本地的 `origin/**` 的分支合到相应的本地分支上，可能有点麻烦，那 `git pull` 其实就是 `git fetch` 和 `git merge` 的结合

以下命令在 Git 中是等效的:

`git pull origin foo` 相当于：

```
git fetch origin foo; git merge o/foo
```

还有 `git pull origin bar~1:bugFix` 相当于：

```
git fetch origin bar~1:bugFix; git merge bugFix
```

可以看到 `git pull` 实际上就是 `fetch + merge` 的缩写, `git pull` 唯一关注的是提交最终合并到哪里（也就是为 `git fetch` 所提供的 `destination` 参数）

