### vimdiff

在**Git**的合并分支过程中，遇到冲突会使用一些合并工具来解决冲突，**vimdiff**就是其中之一，他是**vim**工具的**diff模式**

**Git**使用**VimDiff**显示如下

```bash
  +----------------------+
  |       |      |       |
  |LOCAL  |BASE  |REMOTE |
  |       |      |       |
  +----------------------+
  |      MERGED          |
  |                      |
  +----------------------+
```

- LOCAL – this is file from the current branch

- BASE – common ancestor, how file looked before both changes

- REMOTE – file you are merging into your branch

- MERGED – merge result, this is what gets saved in the repo

简写形式:LO、BA、RE

在**diff模式**中，品红色的部分为相同部分，而红色部分为不同部分；

 #### 打开vimdiff

```bash
$ vimdiff <file> <file>
$ vim -d  <file> <file>

#如果是只打开一个文件，也可以打开vimdiff模式
:diffsplit <filename>			#水平方向
:vert diffsplit <filename>		#垂直方向
```

#### 导航

在diff模式中，在一个文本中滚动，相邻的文本也会跟着滚动

```bash
:set scrollbing				#打开关东绑定
:set noscrollbing			#关闭滚动绑定
:diffupdate					#在这种模式中修改文件后需要更新
```

#### 切换窗口

`Ctrl+w` +`Ctrl+w`，也就是需要两次`Ctrl+w`

#### 移动差异点

```bash
[c			#移动到上一个差异点
]c			#移动到下一个差异点
```

#### 从diff窗口更改内容

```bash
:diffget		#更改当前窗口内容为其他窗口内容
:diffput		#更改其他窗口内容为当前窗口内容
#可简写diffget为diffg
```

#### 同时操作多个文件

```bash
:qa				#(quit all)退出所有文件
:wa				#(write all)保存所有文件
:wqa			#(write then quit all)
:qa!
```

