workflow 针对仓库可以自动构建、测试、打包、发布或者部署任何项目

workflow 文件存储在 `.github/workflow` 仓库根目录，workflow 至少需要一个 job，job 需要包含多 steps 的 task，每个 steps 可以运行命令或者使用一个 action。

```yaml
name: Greet
# 在推送时触发这个工作流
on: [push] 
jobs:
	build:
		name: Greeting # job name
		runs-on: ubuntu-latest # 运行在 ubuntu 上
		steps:
			- name: Hello world
				uses: actions/hello-world-javascript-action@v1 # 使用了 GitHub action 仓库的action
				with:
					who-to-greet: "Mona the Octocat"
				id: hello
			- name: Echo the greeting's time
				run: echo 'The time was ${{ steps.hello.outputs.time }}.'
		
```

#### 事件触发一个工作流

`on:` 选项下制定具体事件

#### 选择workflow 运行的平台

`runs-on` 选项指定具体的主机

#### 配置一个编译矩阵

为了能同时在多个系统，架构和语言版本之间交叉编译，需要配置一个编译矩阵。

`strategy` 选项下指定具体的语言版本和操作系统

#### 使用 actions 仓库

在 `steps` 选项下使用 `uses` 指定要使用的 action，use 的语法是 `owner/repo@ref` or `owner/repo/path@ref`，针对 dockerhub 的引用语法是 `docker://{image}:{tag}`

#### 为 workflow 选择 actions 的类型

- docker container actions
- JavaScript actions

当选择了 actions 的类型后，会自动去发现一些符合当前仓库的 actions

#### 给仓库添加 workflow 状态标记

状态标记展示当前一个 workflow 是否是通过或失败，通常添加在 README.md 中。默认展示的是默认分支（master）的标记，也可展示指定的分支标记，在 URL 中使用 `branch` 和 `event` 查询参数，URL 如下：

使用 workflow 的 name 的 URL：`https://github.com/{OWNER}/{REPOSITORY}/workflows/{WORKFLOW_NAME}/badge.svg`

如果没有指定 workflow 的名字则必须指定文件路径以仓库根目录为相对路径：

`https://github.com/{OWNER}/{REPOSITORY}/workflows/{WORKFLOW_FILE_PATH}/badge.svg`

使用带有 branch 参数的 badge：

`![](https://github.com/actions/hello-world/workflows/Greet%20Everyone/badge.svg?branch=feature-1)`

使用带有 event 参数的 badge：

`![](https://github.com/actions/hello-world/workflows/Greet%20Everyone/badge.svg?event=pull_request)`

