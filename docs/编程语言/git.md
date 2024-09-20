---
tags:
  - no-tag
date: 2024-09-17
publish: true
---
# git

推荐使用 [Sourcetree | Free Git GUI for Mac and Windows](https://www.sourcetreeapp.com/)

经典三段式，add告诉git对哪些文件的变动进行记录（create/delete/modify）兼有暂存文件修改的作用，commit就是创建备份点，push就不多说，修改已经push了的commit加-f。

最为常用：

checkout[[../../58561_Git从入门到精通_高见龙#^e438n025p6k|这个命令会把暂存区（Staging Area）中的内容或文件拿来覆盖工作目录中（Working Directory）的内容或文件。]]

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

# 2.rebase 把从lca开始的修改叠加到某一个commit上
git rebase test
# 解决冲突
git add .
git rebase --continue
```

## Rebase

合并可以用 `git rebase <base commit>`，不仅仅可以合并分支，还可以合并commit。这个过程涉及到以下几个步骤：

1. **确定公共祖先**：当前commit与base commit的lca。
2. **取出更改**：取出当前commit从公共祖先开始的所有提交。
3. **应用更改**：将这些提交更改依次接续到base commit后方。

在 `rebase` 过程中，如果出现冲突，Git 会停止并允许你手动解决这些冲突。解决冲突后，你需要使用 `git add` 将更改加入暂存区，然后使用 `git rebase --continue` 命令继续 `rebase` 过程。

`rebase` 操作的好处是可以将项目的历史变得更加清晰，因为它避免了不必要的合并提交，使得项目的历史看起来像是一条直线。这对于查看项目的历史和维护项目非常有用。

更精细化的修改需要使用 `git rebase -i` 的交互式操作，它允许你指定从公共祖先开始的每个提交分别采取怎样的操作：

1. **pick**：这是默认操作，即按照原来的顺序将lca链上的提交依次应用到base commit后方。
2. **reword**：修改某个提交的消息，这会在应用该提交之前暂停变基过程，允许你编辑提交消息。
3. **squash**：将提交合并到前一个提交中，并让你给合并后的提交提供一个新的提交消息。
4. **fixup**：将提交合并到前一个提交中，沿用前一个提交的消息。
5. **edit**：对某个提交进行修改、添加新的commit或修复错误。这会暂停变基过程，允许你进行所需的更改，然后继续变基。（例子：[[../../58561_Git从入门到精通_高见龙#^gnfh37yab8c|Commit的文件太多了，可能会想把它拆解得更细。]] 用edit暂停，reset到HEAD^，一个一个add+commit）
6. **drop**：这个选项用于删除某个提交。如果你不再需要某个提交，可以将其行的 `pick` 改为 `drop`。

**squash和fixup不能用在rebase -i列表的首个commit，因为它前面就是要保留的base commit，无法与之合并成新commit**

## 其他

git只认文件，空目录需要使用文件.keep/.gitkeep占位。

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

# 立刻 gc
git fsck --unreachable
git reflog expire --all --expire=now
git gc --prune=now 
```



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

PS C:\Users\24171\Documents\GitHub\Playground> git checkout 41cb26f
Note: switching to '41cb26f'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

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

空的目录无法被add检测，因为git add只关心文件内容生成blob对象，git commit后才组织tree对象形成目录。[[../../58561_Git从入门到精通_高见龙#^oxy7q8yy9x|在空目录中随便放一个文件就行了。如果当前还没有文件可以放，或者不知道该放什么文件，通常可以放一个名为“.keep”或“.gitkeep”的空文件，让Git能“感应”到这个目录的存在]]