[toc]

# Window 版的Git

## Git Basic

**Git**由三棵‘树’组成，第一个是`工作目录`,就是你实际操作的目录；第二个是`暂存区（Index）`，它类似于缓存区域，临时保存改动；最后一个就是`HEAD`，它指向你最后的一次提交的结果

在创建仓库的时候，*master* 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上

修改本地仓库上的文件夹名，只要关闭了`Git Bash`就可以操作，如果显示程序忙，可能需要到任务栏把进程关了。

#### 推送改动
`git status`/`git log`查看文件发现中文文件名都为十进制数字，这是文件名被转化了，配置下`git config --global core.quotepath false`就可以了

### 1.安装设置

安装完Git后，在安装目录，有个文件夹`[path]etc/gitconfig`,这个文件夹呢，就是用来给Git设置环境变量的，这个是系统环境变量（针对所有用户所有仓库），只要安装了Git，就肯定有；除了这个，还有全局变量（只针对某个用户的，这个用户的所有仓库），一般在`~/.gitconfig` 或者`~/.config/git/config`；还有本地变量（针对某个用户单一仓库），放在`.git/config`;

```bash
$ git config --list --show-origin   #查看变量的设置
$ git config --list      #查看变量的设置(简易版本)
#查看不同级别的设置，可通过加变量来获取
--system   #系统级别
--globle   #全局级别
--local    #本地级别
```

#### 身份设置
```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

> 通过`git init`可以自动创建一个默认分支`master`

### 2.获取帮助

有三种方法：

```
git help <verb>
git <verb> --help
man git-<verb>
```

### 3.获取一个仓库

- 新建一个文件夹，然后把它变为一个仓库
- 从其他地方克隆一个仓库

#### 在已存在的目录上初始化一个仓库

```bash
#定位到目录
$ cd C:/Users/user/my_project	
#初始化
$ git init
```

初始化之后，在`C:/Users/user/my_project`目录下会有一个`.git`的子目录

#### 版本控制文件

```bash
$ git add *.c
$ git add LICENSE
$ git commit -m 'Initial project version'
```

`git add *`用来指定所需要记录的文件，最后`git commit *`用来一并提交之前所有的所需要记录的文件。

#### 克隆一个存在的仓库

```bash
$ git clone <url>
#示例
$ git clone https://github.com/libgit2/libgit2
#指定文件名的示例
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

克隆仓库，一般会在所操作的目录直接生成一个以\<url>结尾字段为名的仓库，当然也可以自己指定，在\<url> 后面添加参数（文件名）就可以;

##### 克隆他人的仓库

想克隆其他人的仓库，但是发现没办法通过SSH方式克隆，但是呢？使用HTTPS的方式克隆就可以。

- 想通过SSH方式克隆可先fork项目到自己的账号下，再添加远程到本地就可以实现克隆了，也能够推送提交到远程仓库上
- 通过HTTPS方式克隆，无法添加远程到本地，故也就不能够推送提交到远程仓库上了

### 4.检查文件的状态

#### 文件状态的生命周期

每一个文件在工作目录都是两种状态中的一种：记录和未记录

记录：可以是未修改、已修改、暂存的文件

未记录：就是没有放到暂存区的文件

#### 检查文件状态

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean

```

#### 补充：

`git status` 命令显示出来的中文文件档名为八进制的字符编码，如果想要正常显示，命令如下：

```
git config --global core.quotepath false
```

#### Recording Changes to the Repository

![The lifecycle of the status of your files](https://git-scm.com/book/en/v2/images/lifecycle.png)

| 操作                           | 状态                                       |
| ------------------------------ | ------------------------------------------ |
| 新建一个文件（README）在目录中 | Untracked                                  |
| git add README                 | Tracked、Staged、Commited                  |
| vim README                     | Tracked、Modified（UnStaged、UnCommited ） |
| 再次执行 git add README        | Tracked、Staged、Commited                  |

如果新建文件的话就一定是Untracked，已`git add xx`的文件，一定是Tracked；

已`git add xx`的文件，如果再次修改，那么它会出现两种状态：

在暂存区的状态：已提交，已修改

在工作区的状态：为暂存，为提交，已修改

这两种状态就是所谓的版本问题了，暂存区的是上一个已提交的版本，现在在工作区的是当前的新版本，还未提交！

#### 显示状态快捷方式

```bash
$ git status -s			#添加参数 -s
 M README			    #Unstaged Modified
MM Rakefile			    #Staged Modified&Unstaged Modified
A  lib/git.rb           #Staged Added
M  lib/simplegit.rb     #Staged Modified
?? LICENSE.txt          #Untracked
```

#### 忽略文件

> 忽略文件就是不让Git自动添加和显示那些Untracked文件，比如log文件，构建系统产生的文件

```bash
$ cat .gitignore		#新建.gitignore文件
*.[oa]					#忽略任何以.o/.a结尾的文件
*~						#忽略以波浪线为结尾的文件
```

#### Viewing Your Staged and Unstaged Changes

```bash
$ git diff						#What have you changed but not yet staged?
$ git diff --staged/--cached	#what have you staged that you are about to commit
```

### 5.提交修改

```bash
$ git commit			#不写提交信息，Git会自动根据剥离出来的注释和不同来添加提交信息
$ git commit -m "what you want to comment" #通过参数（-m），自己添加提交信息
```

### 6.删除文件

```bash
$ rm PROJECTS.md		#删除暂存区的文件，但是工作区的文件还在
$ git rm PROJECTS.md	#删除暂存区的文件和工作区的文件
$ git rm -f PROJECTS.md #删除已修改并添加到暂存区的文件，需要用-f参数，这是一种安全办法，用于你恢复以后从Git这份文件
$ git rm --cached PROJECTS.md  #删除暂存区的文件但不删除工作区的文件，一般用于删除那些你忘记添加到.gitignore的文件
$ git rm \*~			#删除所有已~结尾的的文件
$ git rm log/\*.log		#删除log文件夹里面的所有以.log结尾的文件
```

> 如果只是删除一个没有修改的文件，那么可以直接`git rm`，他就会删除这个文件的所有信息，如果你修改过这个文件，那么为了以后能恢复这个文件，就需要存储这个文件记录在Git仓库可供你以后恢复这个文件，也就是添加参数`-f `;

### 7.移动文件

```bash
$ git mv READMD.md READMD
#上面的代码类似于如下代码：
$ mv README.md README
$ git rm README.md
$ git add README
```

> 也就是说，`git mv` 再修改文件名之后会自动`git add`文件到暂存区

### 8.查看提交历史

```  bash
$ git log    #列出仓库的所有提交
```

```bash
$ git log -p(--patch) -2 
```

除了显示仓库的提交信息外，参数`-p`还显示不同部分的介绍

`-2`是用来限制输出的提交数量，如果不限制，则全部显示

```bash
$ git log --stat
```

`--stat`这个参数有总结的功能，会显示修改了多少个文件，插入多少，删除多少数据（行）

````bash
$ git log --pretty=online
````

`--pretty`可用来格式化输出，比如传入参数`oneline`，就把每个提交都输出到一行上

除了`oneline`，还有`short`、`full`、`fuller`等等

```bash
$ git log --pretty=format:"%h - %an, %ar : %s"
```

`--format`参数是用来自定义格式化格式的；

`%h`输出缩写的commit hash码；

`%an`输入作者名；`%ar`:作者修改的相关日期；`%s`项目

```bash
$ git log --pretty=format:"%h %s" --graph
```

`--graph`会显示分支和分支合并历史并以图形的格式展示

#### 限制日志输出

```bash
$ git log --since=2.weeks
```

`--since`参数会显示最近两周的提交记录 

`--until`则会显示某个时间段之前的提交记录

```bash
$ git log -S function_name
#区别于git log --grep用于查找提交信息中含有某某关键字的一些提交记录
```

查找匹配到某个字段的提交记录

```bash
git log -- path/to/file   #--用于分割选项和参数
```

只查找某个路径的文件提交记录

> `--no-merge用于去掉合并提交的信息，避免混乱`

### 9.撤销

```bash
$ git commit --amend
```

用于修改提交信息 或 

把新添加到暂存区的文件记录加入到上次的提交信息中

```bash
$ git reset HEAD contributing.md
```

撤销新添加到暂存区的文件记录

```bash
$ git checkout -- CONTRIBUTING.md
```

撤销工作区的修改记录

```bash
$ git restore   #具有上面两种的功能，就是既能撤销工作区的记录，也能撤销暂存区的记录
$ git restore --staged contributing.md  #撤销暂存区的记录
$ git restore contributing.md 			#撤销工作区的记录
```

### 10.使用远程

```bash
$ git remote 			#显示每一个远程的仓库的简称
$ git remote -v			#除了显示简称还显示远程仓库的读取和推送的URL链接
```

推送和读取远程仓库可以多个，也就是说，对于同一仓库，可能有很多人分工合作，所以你需要跟这些人对接，你可能从某个人的版本那拉取数据，也可能推送数据给某个其他人；

```bash
$ git remote add <shortname> <url>  		#添加远程仓库
$ git fetch <remote> <remote-branch>		#拉取远程仓库的更新
$ git pull <shortname> <local branch>		#拉取远程仓库的更新并合并拉取得数据
```

`git fetch `只是拉取数据，并没有合并你的数据和这些拉去的数据，所以需要手动自己合并数据；

```bash
$ git fetch origin master					#拉取远程仓库更新
$ git branch -r/-a							#查看远程分支
$ git merge origin/master					#合并远程分支到本地分支
```

而使用`git pull`则会拉取并合并数据	

```bash
$ git push origin master			#推送本地的master分支数据到远程origin服务器
$ git remote show origin			#查看某个远程的信息
$ git remote rename pb paul			#重命名远程仓库名为paul
$ git remote remove paul			#删除远程仓库
```

### 11.Togging

```bash
$ git tag -l(--list)   				#列出所有标签
$ git tag -l "v1.8.5"				#列出1.8.5系列的标签
$ git tag -a v1.4 -m "my version 1.4"		#注释型的标签
$ git tag v.14						#轻量的标签，没有其他信息
$ git show v.14						#显示标签的内容
$ git tag -a v.12 <commit checksum> #用于给某个提交设置标签
```

如果想列出所有标签，则`git tag`命令就可以了，它隐含地包括`-l`参数，如果使用通配符，则需要显示地使用`-l`参数；

注释型的标签只要加入参数`-a`就可以，不过`-m`参数也需要带上，这就是用来指明标记信息（标添加标签的作者，邮箱，日期，标签信息等等），如果没有带上，`git`会调用编辑器让你输入，啊哈哈哈:ghost:

```bash
$ git push origin v1.5				#推送标签版本到远程服务origin
$ git push origin --tags			#推送多个标签版本到远程服务origin
$ git push <remote> --follow-tags	#推送注释版本的标签
```

> 没有参数用来只推送轻量型的标签

```bash
$ git tag -d v1.4-lw				#删除本地标签（不会删除远程标签）
$ git push origin :refs/tags/v1.4-lw	#删除远程标签
$ git push origin --delete <tagname>	#删除远程标签
```

```bash
$ git checkout v2.0,0				#检查标签
```

检查标签会进入‘detached HEAD’状态，这种状态下修改了即使提交了也不会提交到标签里面，也就是修改提交无效，如果想要保存修改状态，可以使用另一个分支来保存这些修改

### 12.Alias

```bash
$ git config --global alias.co checkout			#给git checkout设置别名
$ git config --global alias.br branch			#给git branch设置别名
$ git config --global alias.unstage 'reset HEAD --'		#给复杂的命令设置别名
$ git config --global alias.visual '!gitk'		#运行外部命令，我还不太懂	
```

<hr>

## Git Branching


### 1.新建分支


```bash
$ git branch testing			#新建分支
$ git log --oneline --decorate	#查看目前位于哪个分支
$ git checkout testing			#切换分支
$ git checkout -b <newbranchname>	#新建分支并切换到新分支
```

### 2.切换分支

> 切换分支会修改工作目录中的文件记录

```bash
$ git switch testing-branch		#切换分支
$ git switch -c new-branch		#新建分支并切换分支
```

### 3.合并分支

```bash
$ git checkout master			#切换到master分支
$ git merge hotfix				#合并hotfix分支到master分支
$ git branch -d hotfix			#删除hotfix分支
```

>如果合并的分支只有一个提交，那么就会使用`fast-forward`来直接合并，如果合并的分支有多个提交，那么就需要使用`recursive Strategy`方法来合并，这种方法其实是通过新建一个提交来指向多路合并，他有多个父级，合并后删除掉分支并不会删除掉数据，因为其实那只是一个快照而已，数据都还是存在了，他被指向了新的合并的快照

```bash
$ git mergetool					
```

如果**合并分支的时候出现冲突**，这时候可以自己修改文件，解决这些冲突；或者是像上面一样，调用解决冲突的方法`git mergetool`来更加便捷地处理冲突

### 4.分支管理

```bash
$ git branch					#显示分支
$ git branch -v					#查看每个分支的最后提交

$ git branch --merged			#显示加入到当前所在分支的分支
$ git branch --no-merged		#显示还没有加入到当前所在分支的分支
$ git branch --no-merged master	#显示没有加入到master分支的分支
```

如果某个分支没有被合并，就被删除，那么Git会提醒您，您还未合并它，如果想强制删除可以用

`git branch -D testing`

### 5.改变分支名

> 当其他合作者正在某个分支上工作时，不能修改该分支名；修改master/main/mainline等分支要注意，请参阅“Changing the master branch name”

```bash
#修改本地分支名
$ git branch --move bad-branch-name corrected-branch-name
#修改远程分支，只是在远程服务器上添加一个新的分支而已，所以原先的分支还在

$ git push --set-upstream origin corrected-branch-name
$ git push --u origin corrected-branch-name
#让本地分支关联远程origin上的某个分支,比如dev
#以后推送只需要给 git push origin就直接推送到origin/dev

$ git push origin --delete bad-branch-name	#删除错误分支
```

#### changing the master branch name

Changing the name of a branch like master/main/mainline/default will break the integrations, services, helper utilities and build/release scripts that your repository uses. Before you do this, make sure you consult with your collaborators. Also make sure you do a thorough search through your repo and update any references to the old branch name in your code or scripts.

> 修改主分支其实跟其他分支一样，只不过它有一些比较麻烦的东西需要处理

```bash
$ git push --set-upstream origin main

git branch --all
* main
  remotes/origin/HEAD -> origin/master
  remotes/origin/main
  remotes/origin/master
```

 You have a few more tasks in front of you to complete the transition: 

• Any projects that depend on this one will need to update their code and/or configuration.

• Update any test-runner configuration files. 

• Adjust build and release scripts. 

• Redirect settings on your repo host for things like the repo’s default branch, merge rules, and other things that match branch names. 

• Update references to the old branch in documentation. 

• Close or merge any pull requests that target the old branch.

只有确保上面这几条条件做好后，才能删除主分支

```bash
$ git push origin --delete master
```

### 6.远程分支

#### Pushing

```bash
$ git push origin serverfix			#默认远程分支为serverfix
$ git push origin serverfix:serverfix
```

以上两条命令功能相同，推送本地分支--serverfix到远程分支--serverfix上

```bash
$ git fetch origin					#从远程分支获取数据
$ git merge origin/serverfix		#合并远程origin.serverfix分支
```

以上从远程分支--serverfix获取数据，如果本地没有这个serverfix分支，本地并不会新建一个新的分支--serverfix，就算有也需要自己合并它们；

最好的办法是自己新建一个serverfix分支并关联它们，如下

```bash
$ git checkout -b serverfix origin/serverfix
```

#### Tracking Branches

其实Tracking Branches就是对于远程分支，在本地的关联分支;

而Upstream则表示远程服务分支

```bash
$ git checkout -b serverfix origin/serverfix
$ git checkout --track origin/serverfix		#简写方式
$ git checkout serverfix			#需要满足条件
```

以上三条命令的功能相同，都是新建关联远程分支--origin/serverfix的本地分支--serverfix，其中第三条命令需要满足的条件是：本地没有serverfix分支，但是有个远程分支名跟serverfix匹配

```bash
$ git checkout -b sf origin/serverfix	#设置别名，而非默认的serverfix
```

```bash
$ git branch -u origin/serverfix
$ git branch --set-upstream-to origin/serverfix
```

修改关联的远程分支或者设置当前分支关联远程分支

```bash
$ git branch -vv			#查看目前本地分支及其远程关联
$ git fetch --all; git branch -vv   #获取最新更新消息
```

使用`git branch -vv`会显示更新的提交数和落后数，但这是根据上次你`git fetch`后获得的远程更新信息，或许这时候已经改变了，故，可以再执行之前，先`git fetch --all`获取最新的更新

#### Pulling

除了使用`git fetch `拉取数据，还可以用`git pull`，再重申一遍，`git pull`拉取数据后会顺便合并分支；不过一般还是使用`git fetch`拉取数据，比较号，`git pull`经常会失败。

#### Delete Remote Branches

```bash
$ git push origin --delete serverfix
```

## Git Branching - Rebasing

其实这是一种跟`git merge`类似功能的分支功能

他的特点是：把分支的提交内容作为新的提交添加到主线分支上，也就是说**在主分支上新添加一个提交**，

```bash
$ git checkout experiment		#定位到其他分支
$ git rebase master				#基准主分支

$ git checkout master			#重新回到主分支
$ git merge experiment			#合并其他分支到主分支
```

> `rebase`跟`merge`不同的点：
>
> 主要在`merge`需要定位到主分支上，然后**以主分支为准**，利用merge 合并其他分支；
>
> 而rebase则是以**其他分支为准**，利用rebase，重新设定主分支

```bash
# 把client分支重新设定到主分支master上，而不改变server分支状态
$ git rebase --onto master server client

#把server分支重新设定到主分支master上
$ git rebase master server
```

> 不管是使用`rebase`还是`merge`都不会删除分支，而只是把分支的内容设定到新的提交上

### rebase的风险

如果参与者在远程分支上新建一个项目，然后你拉取了项目信息，之后它撤销了一些提交后重新设定主分支，那么当你下次再次拉去信息的时候就会出现一些重复性的内容，严重的可能回导致错误发生。具体参考案例在[这里](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

当这种事情发生的时候，可以通过以下方法来让本地分支重新关联修改后(rebase)的远程主分支

```bash
$ git rebase origin/master

$ git pull --rebase

$ git fetch origin/master git rebase origin/master
```

以上三种处理方法的结果都一样，在本地仓库，修改远程为重新设定的状态后，再添加新的提交内容到重新设定主分支上

## github协同办公

### 参与者模式

需要原创者到原始仓库，进入`Setting`中的`Manage access`，然后通过`Invite a collaborate`来邀请参与者，这样，所有同意邀请的参与者就都有权限来推送更新到原始仓库了。

### fork模式

参与者Fork原始github上的仓库到自己仓库，修改后或者更新后，到github官网上，登录自己的账号，然后到自己这个Fork的仓库，然后使用`Pull requests`推送更新到原始仓库；