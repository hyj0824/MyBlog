---
tags:
  - no-tag
date: 2024-09-17
publish: true
---
# git

经典三段式，add告诉git对哪些文件的变动进行记录（create/delete/modify）兼有暂存文件修改的作用，commit就是创建备份点，push就不多说，修改已经push了的commit加-f。

最为常用：

checkout[[../../58561_Git从入门到精通_高见龙#^e438n025p6k|这个命令会把暂存区（Staging Area）中的内容或文件拿来覆盖工作目录中（Working Directory）的内容或文件。]]当然用restore会更直观。

```bash
git checkout test
git checkout .
git checkout HEAD~2 .

# add+利用编辑器确认commit的文件
git commit -a
git push

# 回到过去 reset默认的mixed就很好用
git log --oneline
git reset "sha1"

# reset错了返回未来 用reflog查HEAD的移动记录
git reflog
git reset "sha1"


# 分支的几种方式 所有合并操作都是别的commit->HEAD 且都不会对别的分支产生影响

# 1.merge几个commit 其实pull底层就是把origin/x合并到x
git checkout main
git merge test new
# 解决冲突
git commit -a

# 2.rebase 把分支到某一个commit上
git rebase test
# 解决冲突
git add .
git rebase --continue
```


`rebase -i` 强大无需多言！！！改变历史！！！改天改地！！！它允许你以交互式的方式修改提交历史。当你在进行变基操作时，Git 会提示你一系列的操作选项，其中 `squash`、`reword`、`pick` 是最常见的几种：

1. **pick**：这是默认操作，它会按照原来的顺序将lca链上的提交依次重新应用到基分支上。
2. **reword**：如果你想要修改某个提交的信息（即提交消息），你可以将该提交行的 `pick` 改为 `reword`。这会在应用该提交之前暂停变基过程，允许你编辑提交消息。
3. **squash**：这个选项用于将一个提交合并到前一个提交中。如果你有两个提交并且想要将它们合并为一个，你可以将第二个提交行的 `pick` 改为 `squash`。这会导致这两个提交被合并，并且你会有机会为合并后的提交提供一个统一的提交消息。**作为base的commit不能写squash！**
4. **edit**：这个选项允许你对某个提交进行修改，比如添加新的更改或修复错误。这会暂停变基过程，允许你进行所需的更改，然后继续变基。[[../../58561_Git从入门到精通_高见龙#^gnfh37yab8c|Commit的文件太多了，可能会想把它拆解得更细。]] 用edit暂停，reset到HEAD^，一个一个add+commit。
5. **drop**：这个选项用于删除某个提交。如果你不再需要某个提交，可以将其行的 `pick` 改为 `drop`。
6. **exec**：这个选项允许你在变基过程中执行一个外部命令。这可以用来运行测试、构建项目或其他脚本。

```shell
# commit图
git log --oneline --graph

# rm + git add：删除文件并stage
git rm *

# 取消文件追踪不删除文件 make it untracked
git rm --cached *
git restore --staged *

# reset HEAD^1后再做一次commit，也就是修改最新commit
git commit --amend -m "xxx"
git commit --amend --no-edit # 不修改message

# 记录变动并合并到最新commit
git commit -a --amend --no-edit

# 删除远端分支
git push origin :new

# cherry-pick 仅拉取特定的commit内容，而不是从lca开始的全部commit
git cherry-pick "sha1" "sha2"
```

git只认文件，空目录需要使用文件.keep/.gitkeep占位。

## HEAD

[[../../58561_Git从入门到精通_高见龙#^m77tn7w6d4r|HEAD是一个指标，指向某一个分支，通常可以把它当作“当前所在分支”来看待。]]（如：refs/heads/master）。分支也是个指针，指向分支上最新的commit。例如：HEAD -> refs/heads/master -> b431fa7

HEAD如果没有指向本地的分支，则称之为detached head（断头HEAD）。可能发生这个状态的原因有：

1. 使用 Checkout 指令直接跳到某个 Commit，而不是 Branch。
2. Rebase 的过程其实也是处于不断的 detached HEAD 状态。
3. 切换到某个远端分支的时候。

断头HEAD约等于创建一个叫HEAD的临时分支，可以允许你任意checkout某个commit开始工作，提交新的commit与主干分叉。但要切换到其他分支或commit时，你可以任选：

1. 给这个临时分支一个名字，即创建一个新分支指针指向这个最新的commit，否则就会发生类似memory leak那样的事情。除非在git gc前使用git reflog找到sha1，要不然就找不到了。
2. checkout回原来的branch，然后用sha1来reset。（有点像乘坐时光机到未来hhh）

```powershell
PS C:\Users\24171\Documents\GitHub\Playground> git commit -a
[detached HEAD c599bef]         modified:   note.md
 1 file changed, 3 insertions(+), 6 deletions(-)
PS C:\Users\24171\Documents\GitHub\Playground> git checkout master
Warning: you are leaving 2 commits behind, not connected to
any of your branches:

  c599bef       modified:   note.md
  9a7602e       new file:   note.md

If you want to keep them by creating a new branch, this may be a good time
to do so with:

 git branch <new-branch-name> c599bef

Switched to branch 'master'
```


## .gitignore

以”#”号开头表示注释；
以斜杠“/”开头表示目录；
以星号“\*”通配多个字符；
以问号“?”通配单个字符
以方括号“\[\]”包含单个字符的匹配列表；
以叹号“!”表示不忽略(跟踪)匹配到的文件或目录；

```bash
*.txt , *.xls  # 表示过滤某种类型的文件
target/  # 表示过滤这个文件夹下的所有文件
/test/a.txt , /test/b.xls  # 表示指定过滤某个文件下具体文件
!*.java , !/dir/test/     # !开头表示不过滤
*.[ab]    # 支持通配符：过滤所有以.a或者.b为扩展名的文件
/test  # 仅仅忽略项目根目录下的 test 文件，不包括 child/test等非根目录的test目录
```


## Ref

[[../../58561_Git从入门到精通_高见龙#^v6dqq30vdo|要知道某个文件的某一行代码是谁写的吗？在Git中可使用git blame命令帮你找出来]]

[[../../58561_Git从入门到精通_高见龙#^a1yg1i7urq4|Git根据这串信息，把原本放在.git/objects中以SHA-1计算命名的目录及文件，一个一个地复原成原来的样子，]]

[[../../58561_Git从入门到精通_高见龙#^b5kktkanngd|其实，Git并不是很在意空间的浪费，能够快速、有效率地操作才是Git关注的重点。]]

[[../../58561_Git从入门到精通_高见龙#^o3ed112lpx|Rebase的过程大概是这样的]] Rebase找lca，从lca一层一层重做commit到目标的commit(base)上，遇到冲突就会停下来等，此时你有以下几个选项：

1. **--continue**：这个选项用于在解决冲突或完成某些编辑后继续 rebase 过程。当你解决了所有冲突并测试了代码之后，你可以使用 `git rebase --continue` 来继续应用剩余的提交。
2. **--skip**：如果你遇到了一个无法解决的冲突，或者你决定不应用某个特定的提交，可以使用 `git rebase --skip` 来跳过当前的提交并继续 rebase 过程。
3. **--abort**：这个选项用于完全停止 rebase 过程，并放弃所有已经进行的更改，将 HEAD 重置回 rebase 开始之前的状态。如果提供了 `<branch>`，则 HEAD 会重置到 `<branch>`。否则 HEAD 会重置到 rebase 操作开始时的位置。
4. **--quit**：这个选项用于退出 rebase 会话，但是不重置 HEAD 回原始分支。索引和工作树也会保持不变。如果使用了 `--autostash` 创建了临时 stash，它会保存到 stash 列表中。
5. **--edit-todo**：这个选项允许你在交互式 rebase 中编辑待处理的提交列表。这可以用来改变提交的顺序、修改提交信息、合并提交等。

空的目录无法被add检测，因为git add只关心文件内容生成blob对象，git commit后才组织tree对象形成目录。[[../../58561_Git从入门到精通_高见龙#^oxy7q8yy9x|在空目录中随便放一个文件就行了。如果当前还没有文件可以放，或者不知道该放什么文件，通常可以放一个名为“.keep”或“.gitkeep”的空文件，让Git能“感应”到这个目录的存在]]