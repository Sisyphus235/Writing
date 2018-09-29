# GIT FLOW架构

# 1.GIT传统开发模式
## 1.1 基础
GIT开发有三层框架体系：
（1）Main Repository
（2）Developer Repository
（3）Local Environment

Local GIT版本的三层框架：
（1）History（版本历史）
（2）Stage（暂存区）
（3）Working Directory（工作区）
![图10](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_10.png)

基本操作：
(1）git clone
git clone支持多种协议，包括HTTP, SSH, GIT, 本地协议等。
```
$ git clone <URL>
# 镜像一个版本库，例如git clone https://github.com/xx，默认本地文件夹名字和remote一致
$ git clone <URL> <file_name>
# 镜像一个版本库，本地命名为file_name
```
（2）git remote
git remote用于管理远程主机。
```
$ git remote -v
# 详细显示远程主机情况
$ git remote add <repository_name>
# 添加远程主机名为repository_name
$ git remote rm <repository_name>
# 删除远程主机名为repository_name
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```
（3）git fetch
安全取回远程版本到本地。
```
$ git fetch <repository_name> <branch_name>
# 取回repository_name下branch_name分支的更新，例如：git fetch origin master
```
所取回的更新，在本地以“repository_name/branch_name”形式读取，比如origin的master要用origin/master来读取。
```
$ git branch -r
# 查看远程分支
$ git branch -a
# 查看所有分支
```
取得远程更新后要合并，可以使用git merge或者git rebase
```
$ git merge origin/master
# 或者
$ git rebase origin/master
```
git merge时会在当前分支自动创建一个新的commit object做合并，优点是记录了真实的commit情况，缺点是分支看起来比较杂乱。例如，下图是在feature上创建了一个新的commit object来合并master。

![图11](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_11.png)

git rebase时会合并之前的commit历史，优点是项目更简洁，去掉了merge commit object，缺点是合并代码出现问题不容易定位。例如，下图是在feature上整合了master分支的公共祖先合并成一条线。

![图12](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_12.png)

> 注意：千万不要在别人可见的分支上使用rebase！
私有分支上可以尝试rebase，不熟练的情况下请使用merge

（4）git pull
git pull作用是取回远程主机某个分支的更新，再与本地指定分支合并：
```
$ git pull <repository_name> <remote_branch>: <local_branch>
```
例如：git pull origin master: master，将origin的master取回与本地的master合并。
当冒号后面省略的时候，与当前分支合并，例如：git pull origin master含义是取回origin的master与当前分支合并。
一般建议不直接做pull操作，而是拆解为git fetch和git merge:
```
$ git fetch origin
$ git merge origin/master: master
# 与上面pull代码功能一致
```

（5）git push
git push作用是将本地分支的更新推送到远程主机：
```
$ git push <repository_name> <local_branch>: <remote_branch>
```
例如：git push origin master: master，将本地master推送到origin的master。
如果省略远程分支名，则表示将本地分支推送与之存在“追踪关系”的远程分支（通常是同名的分支），如果不存在这样的远程分支，则会新建。
例如：git push origin master是把本地master推送到远程与之有追踪关系的分支上。

## 1.2 流程
基本项目开发流程如下：
（1）Fork Main Repository到Developer Repository
（2）通过git clone命令镜像到Local Environment
（3）在Local master或新建其他分支上开发
（4）开发完成后push到Developer Repository
（5）审查通过后从Developer Repository merge到Main Repository

## 1.3 问题
当项目开发功能少，开发人员少，使用产品的用户少时，使用基本开发模式有优势，速度快且控制灵活。
随着项目的扩展，研发和用户使用都需要更加强大的GIT FLOW架构，基本框架可能遇到如下问题：
（1）Main Repository不够稳定
developer开发后会直接merge到Main Repository的Master上；
（2）运行版本Bug应急修复不灵活
由于Main Repository的Master记录了所有开发版本，一旦出现Bug，难以迅速找到可以安全回退的版本，且bug修复和开发流程完全一致，容易和开发者产生大量冲突；
（3）版本release不清晰
现有流程难以区分产品的版本，不利于版本控制；
（4）开发优先级不清晰
随着项目的深化，可能需要对feature的优先级进行管理，一部分开发者或许会专注于重要不紧急的feature研发。

# 2. GIT Branching Model管理
## 2.1 基本思想
（1）分离Main Repository的Master分支，以保证运行的安全和稳定性；
（2）运行中一旦出现Bug，能够快速有效的进行Hotfix；
（3）允许对开发优先级的管理，并最小化对现有开发的影响；
（4）支持团队协作，减少多版本之间的conflict，提高开发效率
## 2.2 基本结构
### 2.2.1 稳定存在的branch
（1）Master
主要提供线上产品服务。
（2）Develop
主要负责后台产品研发。
### 2.2.2 临时构建的branch
（1）Hotfix
当产品运行出现bug时，以master现有运行版本为基准创建hotfix branch，实时修复后同时merge到master和release，最后删除该branch。
（2）Release
当开发出稳定的产品需要上线时，以develop需要发布的版本为基准创建release branch，调试minor bug后同时merge到master和release，最后删除该branch。
如果调试bug时遇到较大问题，则merge回develop再行开发。
（3）Feature
以开发者的任务为基准，新建需要的feature branch，开发完成后merge到develop，最后删除该branch。

![图13](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_13.png)

## 2.3 核心框架
### 2.3.1 Master & Develop
master提供运行稳定的产品，通常情况下不是最新版本，master遵循版本控制管理（A.B.C，A是主版本号，代表不兼容的API修改；B是次版本号，代表向下兼容的功能性新增；C是修订号，代表向下兼容的问题修正）。
develop负责研发记录，记录研发版本间的diff，最新版本都从develop上pull。
> master服务用户，develop服务开发者，这样的框架实现了线上线下分离，灵活高效。
### 2.3.2 Feature
> branch off from: develop
merge back to: develop
naming convention: 除了master, develop, hotfix-*，release-*之外，一般以开发feature命名，例如：upload_template

一个developer，每一个feature任务都应该新建一个分支，用来储存相关代码修改。出口主要有两种：1.被使用，merge到develop branch上；2.被放弃，discard即可。

```
$ git checkout -b feature1 develop
# 在develop下创建并切换新分支feature1
$ git add <file1_developed>
# 提交file1_developed到暂存区，记录为"Develop file1"
$ git commit -m "Develop file1"
# 提交file1_developed到版本库，记录为"Develop file1"
$ git checkout develop
# 切换回develop分支
$ git merge --no-ff feature1
# 新建commit object合并feature1到develop
$ git push origin[github上开发者fork过的remote名称] develop
# 将本地develop提交到远程开发者仓库
$ git branch -d feature1
# 删除feature1分支
```
### 2.3.3 Release
> branch off from: develop
merge back to: **develop和master**
naming convention: release-*，例如：release-0.1

当develop branch出现稳定的需要发布的版本，由专人新建一个release分支，用来准备版本发布。出口主要有两种：1.本地测试通过，merge到master和develop上；2.本地测试发现问题较大，放弃release，merge到develop上，交由developer继续开发。

```
$ git checkout -b release-0.1 develop
# 在develop下创建并切换新分支release-0.1
$ git add <minor_bug1>
# 提交minor_bug1到暂存区，记录为"Minor bug1 fixed"
$ git commit -m "Minor bug1 fixed"
# 提交minor_bug1到版本库，记录为"Minor bug1 fixed"
$ git checkout master
# 切换到master分支
$ git merge --no-ff release-0.1
# 新建commit object合并release-0.1到master
$ git tag -a 0.1
# 版本调整为0.1
$ git push origin[github上开发者fork过的remote名称] master
# 将本地master提交到远程开发者仓库
$ git checkout develop
# 切换回develop分支
$ git merge --no-ff release-0.1
# 新建commit object合并release-0.1到develop
$ git push origin[github上开发者fork过的remote名称] develop
# 将本地develop提交到远程开发者仓库
$ git branch -d release-0.1
# 删除release-0.1分支
```
### 2.3.4 Hotfix
> branch off from: master
merge back to: **develop和master**
naming convention: hotfix-*，例如：hotfix-0.1.1

当线上生产机版本出现问题时，从master运行版本新建一个hotfix分支，修改bug后同时提交到master和develop。

```
$ git checkout -b hotfix-0.1.1 master
# 在master下创建并切换新分支hotfix-0.1.1
$ git add <bug1_fix>
# 提交bug1_fix到暂存区，记录为"Serious bug1 fixed"
$ git commit -m "Serious bug1 fixed"
# 提交bug1_fix到版本库，记录为"Serious bug1 fixed"
$ git checkout master
# 切换到master分支
$ git merge --no-ff hotfix-0.1.1
# 新建commit object合并hotfix-0.1.1到master
$ git tag -a 0.1.1
# 版本调整为0.1.1
$ git push origin[github上开发者fork过的remote名称] master
# 将本地master提交到远程开发者仓库
$ git checkout develop
# 切换回develop分支
$ git merge --no-ff hotfix-0.1.1
# 新建commit object合并hotfix-0.1.1到develop
$ git push origin[github上开发者fork过的remote名称] develop
# 将本地develop提交到远程开发者仓库
$ git branch -d hotfix-0.1.1
# 删除hotfix-0.1.1分支
```
# 3. Git常用命令
## 3.1 Git Branching Model
```
创建分支
$ git branch <new_branch_name> [from_branch_name]
# 从from_branch_name创建新分支new_branch_name，如果不写from_branch_name，则创建在当前分支版本
$ git checkout -b <new_branch_name> [from_branch_name]
# 从from_branch_name创建新分支new_branch_name，并切换到new_branch_name，如果不写from_branch_name，则创建在当前分支版本

切换分支
$git checkout <branch_name>
# 切换到branch_name分支

分支交互
$ git add <file1_developed> <file2_developed> ...
# 将file1_developed, file2_developed ...从working directory提交到暂存区，显示版本记录的信息Message
$ git commit -m "Message"
# 将file1_developed, file2_developed ...从working directory提交到版本库，显示版本记录的信息Message
$ git merge [options] <branch_name>
# 按照options要求合并branch_name到当前分支
$ git clean -f
# 如果merge遇到文件冲突，此命令删除不需要修改的文件
$ git reset --<filename>
# 将filename移出文件暂存区，其他文件不变（git add的逆操作）
$ git reset --hard head
# 撤销最近的提交，引用回退到前一次，工作区和暂存区都会会退到上一次提交的状态。

删除分支
$ git branch -d <branch_name>
# 删除branch_name分支
$ git branch -D <branch_name>
# 强制删除branch_name分支
```
## 3.2 远程仓库
```
检出仓库：$ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push [name] [newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
推送远程仓库：$ git push [remoteName] [localBranchName]
```

## 3.3 分支(branch)操作
```
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] 
# 注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：$ git branch -d [name] 
# -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] 
# 将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name]
```
## 3.4 版本(tag)操作
```
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
```

# 4. 实例
在实践实例前，请先整理思路和逻辑，如下图：  

![图14](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_14.png)  

* Github创建[Git_model_branching](https://github.com/Sisyphus235/Git_model_branching)主仓库
* 本地clone主仓库
```
$ git clone https://github.com/Sisyphus235/Git_model_branching
```
![图1](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_1.png)

> 注意：模仿时URL应该是fork过后的URL，如下

```
$ git clone <URL_after_fork>
$ git remote add <main_repository_name> <main_repository_URL>
```

* 建立remote develop branch
```
$ git checkout -b develop
```
![图2](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_2.png)


* local开发feature1
```
$ git checkout -b feature1 develop
$ echo feature1 > feature1.txt
$ git add feature1.txt
$ git commit -m "create feature1"
$ echo feature1 bug fixed > feature1.txt
$ git add feature1.txt
$ git commit -m "feature1 bug fixed"
$ git checkout develop
$ git merge --no-ff feature1
```
![图3](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_3.png)
```
$ git push origin develop
$ git branch -d feature1
```
> 注意：模仿时push后面的origin应该是远程自己仓库的名称，查看远程仓库名称方法如下

```
$ git remote -v
# 查看远程仓库名称
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```

![图4](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_4.png)

* release V0.1
```
$ git checkout -b release-0.1 develop
$ echo feature1 minor bug fixed > feature1.txt
$ git add feature1.txt
$ git commit -m "feature1 minor bug fixed before V0.1 release"
$ git checkout master
$ git merge --no-ff release-0.1
$ git tag -a V0.1
$ git push origin master --tag
```
> 注意：模仿时push后面的origin应该是远程自己仓库的名称，查看远程仓库名称方法如下

```
$ git remote -v
# 查看远程仓库名称
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```

![图5](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_5.png)

```
$ git checkout develop
$ git merge --no-ff release-0.1
$ git push origin develop --tag
$ git branch -d release-0.1
```
> 注意：模仿时push后面的origin应该是远程自己仓库的名称，查看远程仓库名称方法如下

```
$ git remote -v
# 查看远程仓库名称
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```

![图6](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_6.png)

* hotfix紧急bug
```
$ git checkout -b hotfix-0.1.1 master
$ echo feature1 serious bug fixed > feature1.txt
$ git add feature1.txt
$ git commit -m "Serious bug fixed in feaure1.txt"
$ git checkout master
$ git merge --no-ff hotfix-0.1.1
$ git tag -a 0.1.1
$ git push origin master --tag
```
> 注意：模仿时push后面的origin应该是远程自己仓库的名称，查看远程仓库名称方法如下

```
$ git remote -v
# 查看远程仓库名称
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```

![图7](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_7.png)
```
$ git checkout develop
$ git merge --no-ff hotfix-0.1.1
$ git push origin develop --tag
$ git branch -d hotfix-0.1.1
```
> 注意：模仿时push后面的origin应该是远程自己仓库的名称，查看远程仓库名称方法如下

```
$ git remote -v
# 查看远程仓库名称
$ git remote rename <repository_name> <new_name>
# 修改远程主机名repository_name为new_name
```

![图8](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_8.png)

* 前述操作的git graph如下：

```
$ git log --graph
```

![图9](http://p27x0f47q.bkt.clouddn.com/Git_Flow%E6%9E%B6%E6%9E%84_9.png)