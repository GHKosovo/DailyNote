## stast问题

在windows平台使用git add, git commit 文件的时候，经常会出现“warning: LF will be replaced by CRLF”的提示

### 原因

其实，这是因为在文本处理中，[CR](https://en.wikipedia.org/wiki/Carriage_return)（**C**arriage**R**eturn），[LF](https://en.wikipedia.org/wiki/Newline)（**L**ine**F**eed），CR/LF是不同操作系统上使用的换行符

- 回车符就是回到一行的开头，用符号r表示，回车（return）；
- 换行符就是另起一行，用n符号表示，换行（newline）。

所以我们平时在Windows平台编写文件的回车符准确的说应该叫回车换行符。

### 应用情况

- Dos和Windows平台： 使用回车（CR）和换行（LF）两个字符来结束一行，回车+换行(CR+LF)，即`\r\n`；

- MacOS 和 Linux平台：只使用换行（LF）一个字符来结束一行，即`\n`；

### 影响

- 一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；

- 而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。

- Linux保存的文件在windows上用记事本看的话会出现黑点。

这些问题都可以通过一定方式进行转换统一，例如，在linux下，命令`unix2dos` 是把linux文件格式转换成windows文件格式，命令`dos2unix `是把windows格式转换成linux文件格式。

### Git 的处理方式

#### 概念：

1. **标准化** 指在提交代码到git数据库(本地库) 中将文本文件中的换行符CRLF转为LF的过程
2. **转换** 指在检出Git数据库代码过程中将文本文件中的换行符LF转换为CRLF的过程

#### core.autocrlf & core.safecrlf

Git 提供了一个名为 `core.autocrlf` 的配置，可以自动完成标准化与转换。它的设置方式如下：

```shell
git config --global core.autocrlf  [true | input | false]  # 全局设置
git config --local core.autocrlf  [true | input | false] # 针对本项目设置
```

- **true** 自动完成标准化与转换
- **input** 只做标准化操作，不做转换操作
- **false** 提交与检出的代码都保持文件原有的换行符不变

> 1. CRLF 与 LF 混合的文本文件不受此配置控制。
> 2. Git 安装后默认为 false

所以，一种规范换行符的方式是这样的：

使用 Windows 系统的开发者设置：

```shell
git config --global core.aurocrlf true
```

使用 Linux/MacOS 的开发者设置：

```shell
git config --global core.autocrlf input
```

由于没有一个绝对有效的算法来判断一个文件是否为文本，所以Git 提供了一项禁止/警告不可逆转换的配置来防止错误的标准化与转换。它主要是影响到多种换行符混合的文件，我们可以手动将其转换为同一种换行符：

- **true** 禁止提交混合换行符的文本文件(`git add` 的时候会被拦截，提示异常)

- **warn** 提交混合换行符的文本文件的时候发出警告，但是不会阻止 `git add` 操作

- **false** 不禁止提交混合换行符的文本文件（默认配置）

#### .gitattributes 文件

core.autocrlf 的配置依赖于每一位参与项目的开发机器上的配置，这很难确保每个人都能正确配置。于是在规范项目中的换行符方面，还有一套添加配置文件的方案。在项目的根目录下可以添加一个.gitattributes 文件。它的优先级高于core.autocrlf的设置，可以覆盖core.autocrlf的。它类似于 .gitignore 文件，随提交修改生效，一个项目中可以维持一份相同的配置。所以，它能够避免每个开发人员配置不同的问题。

.gitattributes文件的功能不只有配置换行符，所以它的配置相对复杂一下。详细的说明文档可以参考 [地址](http://schacon.github.io/git/gitattributes.html)。这里只针对换行符的配置做一下简单的介绍：

每行基本形式：

```swift
filter attr1 attr2 ....
```

filter 代表匹配文件的通配符，在它后面跟着相应的属性，用空格间隔。

filter 的选项比较简单，常见的：

```css
* 匹配所有文件
*.txt  匹配文件名以txt结尾的文件
```

attr的选择比较多，其中与换行符相关的属性只有几条：

**text**

- **text** 自动完成标准化与转换
- **-text** 不执行标准化与转换
- **text=auto** 根据 Git 决定是否需要执行标准化与转化
- **不设置** 使用core.autocrlf配置决定是否执行标准化与转换

**eol**

- **eol=lf** 强制完成标准化，不执行转换（相当于指定转换为LF格式）
- **eol=crlf** 强制完成标准化，指定转换为CRLF格式

**binary**

- **binary** 二进制文件不参与标准化与转换
- **不设置** 由 Git 决定是否为二进制文件

> text 设置的时候，转换自动转换到对应平台的换行符
> 行号高的设置会覆盖行号低的设置

这里给出一个简单的例子来说明一下：

```bash
*         text=auto
# These files are text and should be normalized (convert crlf => lf)
*.cs      text
*.xaml    text
*.csproj  text
*.sln     text
*.tt      text
*.ps1     text
*.cmd     text
*.msbuild text
*.md      text

# Images should be treated as binary
# (binary is a macro for -text -diff)
*.png     binary
*.jepg    binary

*.sdf     binary
```

除了下面匹配到的文件，剩下的依赖Git 决定是否参与标准化与转换。上面一段是参与标准化与转换的文件；下面一段是不参与标准化与转换的文件；