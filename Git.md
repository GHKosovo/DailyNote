**Git**由三棵‘树’组成，第一个是`工作目录`,就是你实际操作的目录；第二个是`暂存区（Index）`，它类似于缓存区域，临时保存改动；最后一个就是`HEAD`，它指向你最后的一次提交的结果

在创建仓库的时候，*master* 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上

### Git命令

- 创建新仓库：git init

- 克隆仓库：git clone /path/to/repository

（远程仓库）git clone username@host:/path/to/repository

(我觉得这个clone命令 就是把仓库里面的项目拷贝到本地共用户操作使用)

#### 推送改动

- 提交到远程仓库：git push origin master(master可以换为其他分支)

- 将仓库连接到远程服务器：git remote add origin \<server>

- 重新设置远程仓库：

  - 法一：git remote set-url origin \<server>

  - 法二：

    - git remote rm origin 

    - git remote add origin \<server>

#### 分支

- 新建分支并切换为新分支：git checkout -b \<branch>
- 切换回主分支：git checkout master
- 删掉分支：git branch -d \<branch>

- 推送分支到远端仓库：git push origin \<branch>

#### 更新与合并

- 更新本地仓库到最近改动：git pull

- 合并其他分支到当前分支：git merge \<branch>

- 预览差异：git diff \<source_branch> \<target_branch>

#### 标签

- 创建标签：git tag 1.0.0 1b2e1d63ff（1b2e1d63ff是想要标记的提交ID的前十位字符）
- 获取提交ID：git log

#### 替换本地改动

- 替换掉本地改动：git checkout -- \<filename>

  > 此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。


丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

- git fench origin 

- git reset --hard origin/master

<hr>
### 补充：

`git status` 命令显示出来的中文文件档名为八进制的字符编码，如果想要正常显示，命令如下：

```
git config --global core.quotepath false
```



### Recording Changes to the Repository

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

### 显示状态快捷方式

```bash
$ git status -s			#添加参数 -s
 M README			    #Unstaged Modified
MM Rakefile			    #Staged Modified&Unstaged Modified
A  lib/git.rb           #Staged Added
M  lib/simplegit.rb     #Staged Modified
?? LICENSE.txt          #Untracked
```

### 忽略文件

> 忽略文件就是不让Git自动添加和显示那些Untracked文件，比如log文件，构建系统产生的文件

```bash
$ cat .gitignore		#新建.gitignore文件
*.[oa]					#忽略任何以.o/.a结尾的文件
*~						#忽略以波浪线为结尾的文件
```

### Viewing Your Staged and Unstaged Changes

```bash
$ git diff						#What have you changed but not yet staged?
$ git diff --staged/--cached	#what have you staged that you are about to commit
```

### 提交修改

```bash
$ git commit			#不写提交信息，Git会自动根据剥离出来的注释和不同来添加提交信息
$ git commit -m "what you want to comment" #通过参数（-m），自己添加提交信息
```

### 删除文件

```bash
$ rm PROJECTS.md		#删除暂存区的文件，但是工作区的文件还在
$ git rm PROJECTS.md	#删除暂存区的文件和工作区的文件
$ git rm -f PROJECTS.md #删除已修改并添加到暂存区的文件，需要用-f参数，这是一种安全办法，用于你恢复以后从Git这份文件
$ git rm --cached PROJECTS.md  #删除暂存区的文件但不删除工作区的文件，一般用于删除那些你忘记添加到.gitignore的文件
$ git rm \*~			#删除所有已~结尾的的文件
$ git rm log/\*.log		#删除log文件夹里面的所有以.log结尾的文件
```

> 如果只是删除一个没有修改的文件，那么可以直接`git rm`，他就会删除这个文件的所有信息，如果你修改过这个文件，那么为了以后能恢复这个文件，就需要存储这个文件记录在Git仓库可供你以后恢复这个文件，也就是添加参数`-f `;

### 移动文件

```bash
$ git mv READMD.md READMD
#上面的代码类似于如下代码：
$ mv README.md README
$ git rm README.md
$ git add README
```

> 也就是说，`git mv` 再修改文件名之后会自动`git add`文件到暂存区