# git常用命令

| 命令                                           | 说明                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| git clone -b xxxx  .git                        | 克隆仓库，-b 参数可一个指定克隆远程分支                      |
| git commit -am "xxx"                           | git会自动将工作目录中所有“已跟踪”的文件先add到暂存区，再执行commit命令 |
| git commit --amend                             | 修改最后一次提交的注释信息                                   |
| git log --decorate --all --graph --online      | 查看分支合并图                                               |
| git reset --hard commit-id                     | 移动HEAD指向，将快照回滚到暂存区，并将暂存区文件还原到工作目录;**谨慎使用** |
| git reset --soft commit-id                     | 只移动HEAD指向，但不会将快照回滚到暂存区                     |
| git reflog                                     | 查看所执行过的指令                                           |
| git mv old new                                 | 移动并且还可重命名                                           |
| git rm filename                                | 从工作区和暂存区删除该文件，如果想要彻底删除再执行git reset --soft HEAD~ 将快照回滚到上一个位置，然后重新提交 |
| git rm --cached                                | 只删除暂存区文件，取消追踪                                   |
| git rm -r --cached dirname/filename            | 删除远程文件，保留本地文件，再git commit,最后git push        |
| git checkout -- filename                       | 恢复被删除的文件                                             |
| git checkout -b name                           | 表示创建并切换到name分支                                     |
| git checkout -b branch-name origin/branch-name | 创建本地和远程对应的分支                                     |
| git branch name                                | 创建分支                                                     |
| git checkout name                              | 切换分支                                                     |
| git branch                                     | 列出所有分支，当前分支会有*                                  |
| git merge name                                 | 合并name分支到当前分支                                       |
| git branch --merged                            | 显示所有已经合并到你当前分支的列表                           |
| git branch --no-merged                         | 显示所有没有合并到你当前分支的分支列表                       |
| git branch -d name                             | 删除分支，D强制删除分支                                      |
| git branch -r                                  | 查看远程分支                                                 |
| git branch -r -d origin/branch-name            | 删除远程分支，再推送                                         |
| git checkout --track origin/branch-name        | 跟踪指定上游分支                                             |
| git branch -u origin/branch-name               | 修改本地分支正在跟踪的上游分支                               |
| git stash                                      | 把当前工作储藏                                               |
| git stash list                                 | 查看储藏列表                                                 |
| git stash apply name                           | 恢复储藏                                                     |
| git stash drop name                            | 删除储藏                                                     |
| git stash show -p name \| git apply -R         | 取消储藏，name不指定，Git会选择最近的储藏                    |
| git tag                                        | 查看所有标签                                                 |
| git tag -a tag-name -m 'xxxx'                  | 含附注的标签，tag-name：版本号，有私钥可以用GPG将-a变为-s    |
| git tag -v tag-name                            | 验证标签                                                     |
| git push origin tag-name                       | 分享标签，git push 默认不推送标签，推送所有本地标签用--tags  |
| git show tagname                               | 查看标签信息                                                 |
| git tag -d tagname                             | 删除本地标签                                                 |
| git stripsapce < README.md                     | 去掉行尾空白符，多个空行压缩成一行，必要时在文件末尾增加一个空行 |
| git show :/query                               | 查询之前所有提交信息，找到条件相匹配的最近一条，query是想搜索的词语，区分大小写，q键退出 |
| git remote add name url                        | 添加远程仓库 name为自定义主机名 url为git远程仓库地址         |
| git update-index --assume-unchanged PATH       | 忽略指定的文件或目录，由于这个文件是被版本库追踪的且是共有的，使用这个命令会忽略本地的修改，这样可以使的不会影响他人 |

**.gitignore语法规范**

.gitignore可以使用标准的glob模式匹配（glob模式是指shell所使用的简化了的正则表达式）：

- 所有空行或者以注释符号#开头的行都会被Git忽略；

- 星号（*）匹配零个或多个任意字符；

- [abc]匹配任何一个列在方括号中的字符；

- 问好（?）只匹配一个任意字符；

- [a-z]匹配所有在这两个字符范围内的字符；

- 匹配模式最后跟反斜杠（/）说明要忽略的是目录；

- 匹配模式以反斜杠（/）开头说明防止递归；
   要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反	


**Git配置**

打开~/.gitconfig文件添加内容：

```git
[alias]
  co = checkout
  cm = commit
  p = push
  # Show verbose output about tags, branches or remotes
  tags = tag -l
  branches = branch -a
  remotes = remote -v
```

或者

`git config --global alias.cm commit`

指向多个命令的别名可以用引号定义：

`git config --global alias.ac 'add -A . && commit'`







**开发一个项目需要创建的分支**

> 开发一个项目需要创建的分支
>
> Master(主分支)：通常用于对外发布项目的新版本
>
> Hotfix(维护分支)：用于bug修补，属于临时分支用完之后及时删除
>
> tips1：维护分支应该从主分支分离，bug被修补后 再合并到主分支和开发分支
>
> tips2：维护分支可以采用fixbug-*形式命名
>
> Release(预发布分支)：通常用于内部或公开测试，可以从开发分支分离出预发布分支
>
> tips1：预发布分支应该同时合并到主分支和开发分支
>
> tips2：预发布分支可以采用release-*形式命名
>
> Develop(开发分支)：通常用于日常开发
>
> Feature(功能分支)：每一个新功能应该使用一个单独的功能分支开发，从开发分支分离出来，开发完成在合并到开发分支
>
> tips1:功能分支不应该和master分支有任何交流
>
> tips2：功能分支可以采用feature-*的形式命名

| Git命令                            | 说明                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| git merge                          | 合并分支，git merge {branch} 在当前所在分支把 branch 拉过来  |
| `git rebase`                       | 合并分支，可以创造线性的提交历史，git rebase {branch}，将当前所在的分支合并到 {branch} |
| `git checkout HEAD`                | 改变 head 指向可使用 `HEAD^` 或 `HEAD~num`                   |
| `git rebase --interactive HEAD^`   | 交互式 rebase 可以选择HEAD内排序方式 --interactive 简写 -i   |
| `git branch -f 分支名 hash值`      | -f 参数强制改变分支的指向，hash 值 在 git log 可以查看，git branch -f {branch} HEAD~3，将 branch 分支强制移动到了 HEAD位置的前3个位置上，也就表示此时的branch分支指向的位置在 HEAD 的前3个上 |
| `git reset HEAD^`                  | 撤销更改本地可见                                             |
| `git revert HEAD^`                 | 撤销更改（不是直接指向），会创建副本，此副本与父节点一致     |
| `git cherry-pick hash 值`          | 整理提交，可以选择需要其他分支的那些提交，git cherry-pick c3 c4 c8，表示将 c3 c4 c8 合并到当前分支下 |
| `git tag 标签 hash值`              | 给指定提交添加标签                                           |
| `git describe ref`                 | ref 是能被git识别的记录引用，如果没有指定 Git 会以 HEAD 为基准点，输出结果为 tag_numCommits_ghash ，tag 表示是离 ref 最近的标签，numCommits 表示 ref 与 tag 相差有多少个提交，hash 表示 ref hash 值的前几位，当有标签时只输出标签 |
| git push origin source:destination | 表示将 source^(^可以指定提交)分支的内容 推送到远程的 destination 分支去 |

#### git rebase 的操作

主分支

分支 a

分支 b

从主分支切换到分支a进行rebase合并

```
git checkout a
git rebase master // 将分支a合并到主分支
// 如遇到冲突解决后 git add xxx，然后 git rebase --continue
// rebase --continue 作为 commit 命令
// rebase --abort 取消 rebase
```



> cat .git/HEAD 可以查看当前的HEAD 指向，如果 HEAD 指向了一个引用，可以用 git symbolic-ref HEAD 查看它的指向

```
相对引用 ^ 向上移动 1 个提交记录
~num 向上移动多个提交记录 如：~3
```

| 命令                                  | 说明                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| git checkout -b local-branch o/branch | 这样可以使得本地的分支绑定远程对应的分支                     |
| git branch -u o/master local-branch   | 同上的功能，如果是想绑定当前分支到远程分支上，还可以省略本地分支参数 |
|                                       |                                                              |

`git push 远程主机名（一般都是 origin）本地分支:远程分支` 

 `git push origin test` 表示推送到远程分支，并且在远程仓库创建一个 test 分支，等于 `git push origin test:test`

`git push origin test:master` 表示推送 test 分支到 master 分支

git pull 同理

