# GIT与SSH-KEY的一夫多妻制

标签（空格分隔）： 未分类

---

## 引言  
git是Linus的伟大作品，名字背后有一段故事：    
> The name "git" was given by Linus Torvalds when he wrote the very
first version. He described the tool as "the stupid content tracker"
and the name as (depending on your way):
 - random three-letter combination that is pronounceable, and not actually used by any common UNIX command. The fact that it is a mispronunciation of "get" may or may not be relevant.
 - stupid. contemptible and despicable. simple. Take your pick from the dictionary of slang.
 - "global information tracker": you're in a good mood, and it actually works for you. Angels sing, and a light suddenly fills the room.
 - "goddamn idiotic truckload of sh*t": when it breaks

Linus的作品精益求精，每一行代码甚至每一个字段名都被仔细雕琢过，但名字竟可能是一个误读，get误读成了git。  

git的著名“同性交友”网站主要包括github和gitlab，随着微软收购github，越来越多的人迁移至gitlab，为git配置多个SSH-KEY变成一种趋势。

## 配置  
### 1.ssh-keys
mac中git的后宫们在“~/.ssh”中住着。在terminal中  
```
$ cd ~/.ssh
$ ls -l
# 在列表中有一个文件叫known_hosts
$ cat known_hosts
# 可以看到当前ssh-keys连接到的hosts，例如常见的github.com
```
### 2.生成密钥  
xxx@xxx.com是注册github或者gitlab的邮箱
```
$ ssh-keygen -t rsa -C xxx@xxx.com -b 4096
```
之后会提示输入文件名，可以用gitlab或者github，紧接着会询问是否需要passphrase，如果环境安全则不需要设置。

### 3.复制密钥  
密钥会生成公私钥两个文件，分别是文件名 + 文件名.pub，接下来需要复制公钥到gitlab或者github的ssh-keys准备连接。
```
$ pbcopy < ~/.ssh/xxx.pub
```
如果使用nano, cat, vim等进行复制，请注意复制的完整性。

### 4.配置config
在~/.ssh路径下设置config配置文件。例如：  
```
# gitlab
Host gitlab
    HostName gitlab.com
    IdentityFile ~/.ssh/gitlab

# github
Host github
    HostName github.com
    IdentityFile ~/.ssh/github
```
Host后面名称是一个alias，可以随意起名，重要的是IdentityFile的路径。  

### 5.添加ssh-agent
将生成的gitlab或者github私钥路径添加进入ssh-agent  
```
$ ssh-agent -s
$ ssh-add ~/.ssh/xxx
```  
### 6.测试
-T代表测试，-v代表详细信息(verbose)，使用下面命令对配置进行测试
```
$ ssh -Tv git@github.com
$ ssh -Tv git@gitlab.com
```
如果成功则会显示“welcome ... ”或者"Hi ..."；如果未成功使用下面命令查看配置的key，检查gitlab和github配置是否生效。
```
ssh-add -l
```
