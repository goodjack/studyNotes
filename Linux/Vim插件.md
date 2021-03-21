### vim 内置的包管理

在 `.vim` 目录内新建一个 `pack` 子目录，子目录下包含两个文件夹：

- `start` ：目录下的插件会被 vim 自动加载，最佳配置是在 `start` 目录下再新建一个文件夹
- `opt`：目录下的插件是可选的需要手动加载

vim 的包管理可以搭配 git 的 submodule 使用，这样方便添加和升级第三方插件。

将 `.vim` 目录做成一个 git 仓库，使用 `git submodule init` 初始化 submodule

添加第三方插件时就可以使用，`git submodule add {插件的 Git 地址} pack/plugins/插件名`

升级插件，`git submodule update --recursive --remote`

在新机器上配置 vim 时，只需要把仓库 clone 下来，`git clone --recursive {git 地址} ~/.vim`

**子模块未下载全：`git submodule update --init --recursive`**

选择性使用插件时，使用 `packadd 插件名`

删除一个子模块：

- `git submodule deinit -f {子模块的安装路径}`
- `rm -rf .git/modules/xx/子模块目录`
- `git rm -f {子模块安装目录}`

### NERDTree（目录树） 常用快捷键

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| h j k l  | 移动光标定位                                                 |
| ctrl+w+w | 光标在左右窗口切换                                           |
| ctrl+w+r | 切换当前窗口左右布局                                         |
| ctrl+p   | 模糊搜索文件                                                 |
| gT       | 切换到前一个 tab                                             |
| g t      | 切换到后一个 tab                                             |
| o        | 打开关闭文件或者目录，如果是文件的话，光标出现在打开的文件中 |
| O        | 打开节点下的所有目录                                         |
| X        | 合拢当前节点的所有目录                                       |
| x        | 合拢当前节点的父目录                                         |
| i 和 s   | 水平分割或纵向分割窗口打开文件                               |
| u        | 打开上层目录                                                 |
| t        | 在标签页中打开                                               |
| T        | 在后台标签页中打开                                           |
| p        | 到上层目录                                                   |
| P        | 到根目录                                                     |
| K        | 到同目录第一个节点                                           |
| J        | 到同目录最后                                                 |
| m        | 显示文件系统菜单（添加、删除、移动操作）                     |
| q        | 关闭                                                         |



#### vim-surround

ds（delete a surrounding） 删除一个配对符号

cs（change a surrounding） 替换一个成对的符号

ys (yank a surrounding)  增加一个配对符号