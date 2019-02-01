# git常用命令

| 命令                                                        | 说明                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| git init                                                    | git仓库初始化                                                |
| git clone -b xxxx  .git                                     | 克隆仓库，-b 参数可一个指定克隆远程分支                      |
| git add filename[,filename] \| -A                           | 添加一个或者多个，-A表示添加全部                             |
| git commit -m "xxx"                                         | 添加本次提交说明                                             |
| git commit -am "xxx"                                        | git会自动将工作目录中所有“已跟踪”的文件先add到暂存区，再执行commit命令 |
| git commit --amend                                          | 修改最后一次提交的注释信息                                   |
| git status                                                  | 查看仓库当前状态                                             |
| git log                                                     | 查看历史记录                                                 |
| git log --decorate --all --graph --online                   | 查看分支合并图                                               |
| git reset --hard commit-id                                  | 移动HEAD指向，将快照回滚到暂存区，并将暂存区文件还原到工作目录;**谨慎使用** |
| git reset --soft commit-id                                  | 只移动HEAD指向，但不会将快照回滚到暂存区                     |
| git reflog                                                  | 查看所执行过的指令                                           |
| git mv old new                                              | 移动并且还可重命名                                           |
| git rm filename                                             | 从工作区和暂存区删除该文件，如果想要彻底删除再执行git reset --soft HEAD~ 将快照回滚到上一个位置，然后重新提交 |
| git rm --cached                                             | 只删除暂存区文件，取消追踪                                   |
| git rm -r --cached dirname/filename                         | 删除远程文件，保留本地文件，再git commit,最后git push        |
| git checkout -- filename                                    | 恢复被删除的文件                                             |
| git checkout -b name                                        | 表示创建并切换到name分支                                     |
| git checkot -b branch-name origin/branch-name               | 创建本地和远程对应的分支                                     |
| git branch name                                             | 创建分支                                                     |
| git checkout name                                           | 切换分支                                                     |
| git branch                                                  | 列出所有分支，当前分支会有*                                  |
| git merge name                                              | 合并name分支到当前分支                                       |
| git branch --merged                                         | 显示所有已经合并到你当前分支的列表                           |
| git branch --no-merged                                      | 显示所有没有合并到你当前分支的分支列表                       |
| git branch -d name                                          | 删除分支，D强制删除分支                                      |
| git branch -r                                               | 查看远程分支                                                 |
| git branch -r -d origin/branch-name                         | 删除远程分支，再推送                                         |
| git branch --set-upstream-to=origin/branch-name branch-name | 建立本地和远程分支的关联（初始化仓库时需要先 git pull 下）   |
| git stash                                                   | 把当前工作储藏                                               |
| git stash list                                              | 查看储藏列表                                                 |
| git stash apply name                                        | 恢复储藏                                                     |
| git stash drop name                                         | 删除储藏                                                     |
| git stash show -p name \| git apply -R                      | 取消储藏，name不指定，Git会选择最近的储藏                    |
| git tag                                                     | 查看所有标签                                                 |
| git tag -a tag-name -m 'xxxx'                               | 含附注的标签，tag-name：版本号，有私钥可以用GPG将-a变为-s    |
| git tag -v tag-name                                         | 验证标签                                                     |
| git push origin tag-name                                    | 分享标签，git push 默认不推送标签，推送所有本地标签用--tags  |
| git show tagname                                            | 查看标签信息                                                 |
| git tag -d tagname                                          | 删除本地标签                                                 |
| git stripsapce < README.md                                  | 去掉行尾空白符，多个空行压缩成一行，必要时在文件末尾增加一个空行 |
| git show :/query                                            | 查询之前所有提交信息，找到条件相匹配的最近一条，query是想搜索的词语，区分大小写，q键退出 |
| git remote add name url                                     | 添加远程仓库 name为自定义主机名 url为git远程仓库地址         |
|                                                             |                                                              |

**git文件比较**

`git diff`		比较暂存区与工作目录的文件内容

`git diff commit-id`		任意一分快照和当前目录的内容进行比较

![img](E:/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/mxyfreedom@163.com/c169eae8e9744ca2acd4262423543cdd/clipboard.png) 

diff --git a/README..md  b/README.md

表示对比的是存放在暂存区的README.md 和工作目录的README.md

index 5781b27.. a4ad2ea 100644

表示对应文件的ID分别是5781b27和a4ad2ea，左边暂存区，右边当前目录

最后的100644是指定文件的类型和权限

![img](E:/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/mxyfreedom@163.com/08e63fc9ea924035940f5289c31274c9/clipboard.png) 

--- a/README.md

---表示该文件是旧文件（存放在暂存区）

+++ b/README.md

+++ 表示该文件是新文件（存放在工作区）

@@-1+1,2@@

以@@开头和结束，中间的“-”表示旧文件，“+”表示新文件，后边的数字表示“开始行号，显示行数

\ No newline at end of file

表示文件不是以换行符结束



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
| `git rebase`                       | 合并分支，可以创造线性的提交历史                             |
| `git checkout HEAD`                | 改变 head 指向可使用 `HEAD^` 或 `HEAD~num`                   |
| `git rebase --interactive HEAD^`   | 交互式 rebase 可以选择HEAD内排序方式 --interactive 简写 -i   |
| `git branch -f 分支名 hash值`      | -f 参数强制改变分支的指向，hash 值 在 git log 可以查看       |
| `git reset HEAD^`                  | 撤销更改本地可见                                             |
| `git revert HEAD^`                 | 撤销更改，会创建一个当前节点的一个副本，此副本与父节点一致   |
| `git cherry-pick hash 值`          | 整理提交，可以选择需要其他分支的那些提交                     |
| `git tag 标签 hash值`              | 给指定提交添加标签                                           |
| `git describe ref`                 | ref 是能被git识别的记录引用，如果没有指定 Git 会以 HEAD 为基准点，输出结果为 tag_numCommits_ghash ，tag 表示是离 ref 最近的标签，numCommits 表示 ref 与 tag 相差有多少个提交，hash 表示 ref hash 值的前几位，当有标签时只输出标签 |
| git push origin source:destination | 表示将 source^(^可以指定提交)分支的内容 推送到远程的 destination 分支去 |

```
相对引用 ^ 向上移动 1 个提交记录
~num 向上移动多个提交记录 如：~3
```

`git push 远程主机名（一般都是 origin）本地分支:远程分支` 

 `git push origin test` 表示推送到远程分支，并且在远程仓库创建一个 test 分支，等于 `git push origin test:test`

`git push origin test:master` 表示推送 test 分支到 master 分支

git pull 同理