[TOC]

# 一、概览

## 功能

Step Into (`F7`)：执行下一步，不会跳过子函数

Step Over(`F8`)：执行下一步，会跳过子函数

Stop (`Ctrl+F2`)：停止Debug模式

Resume Program(`F9`)：恢复Debug会话， click ![the Resume button](https://resources.jetbrains.com/help/img/idea/2021.1/icons.actions.resume_dark.svg) 

Rerun (`Ctrl+F5`)：重新运行debugger会话

Pause Program：click ![the Pause button](https://resources.jetbrains.com/help/img/idea/2021.1/icons.actions.pause_dark.svg)



### Breakpoint

Toggle line breakpoint: `Ctrl+F8`

Edit breakpoint properties: `Ctrl+Shift+F8`

Open View Breakpoints: `Ctrl+Shift+F8`

## 模式

### Set Value

前提：当运行到某一步，想要返回去执行某个方法，可以通过Resume功能，返回去赋值执行某个功能

1. 当运行到某一点，想回去，在Variable 面板上，打开某个变量，设置值，让该变量刚好能够被某个方法捕获
2. 在这个方法上，设置断点
3. 按F9，执行Resume Program功能恢复Debug会话，程序会恢复并被那个方法捕捉到。

### Remote debug

任意一台IDEA都可以使用Remote Debug模式

具体步骤：

1. Run/Debug Configurations（Alt+Shift+F10 then 0）中，点击Add New Configuration--`+`按钮，选择Remote.

2. 填写Debug配置文件，参数列表

   Configure/use the following properties:

   - **Name**: configure how this run configuration will be called. The name can be anything, including the default value.
   - **Host**: the address of the machine where the host app will run. Since we are running it on the same machine, it needs to be **localhost**. If the program was running on another machine, we would specify its address here, for example: **192.168.17.43**.
   - **Command line arguments for remote JVM**: the VM options that the host application needs to be started with. We will use them in the other run/debug configuration. You can copy them now or get back to this field later.

3. 把**Command line arguments for remote JVM**的信息填写到主程序的配置文件中VM options的选项中，

4. `Alt+Shift+F10`选择刚刚的主配置文件，启动主程序

   > 此时，主程序就已经在运行中了

5. 给主程序的代码中添加断点。

6. Debug访问程序：`Alt+Shift+F9`，选择Debug的配置文件

   > 此时，主程序，暂停了，然后进入debug模式

7. 在Debug的工具窗口中，选择Debug的栏目，可以关闭Debug模式，或者中断这个远程连接。

### Detect concurrency issues

检测并发问题，这个比较难搞，因为这种问题并不是每次运行程序都会出现的，可能运行了几千次才会出现一个问题，那就很难搞了。

1. 首先在容易出现并发问题的代码中，设置断点
2. 然后在断点这里右键选择暂停的内容为Thread，也就是不是全部暂停，而是某一个Thread暂停
3. 以Debug模式运行程序，然后在Frames/Threads的栏目中，选择另一个线程
4. 恢复线程，按`F9`或点击![the Resume button](https://resources.jetbrains.com/help/img/idea/2021.1/icons.actions.resume.svg)，当执行完另一个线程后，
5. 同样的，按`F9`恢复原先的main线程，这时候可以看到两个线程都执行了。

> 官方[示例](https://www.jetbrains.com/help/idea/detect-concurrency-issues.html#reproduce)

## Run/debug configurations

#### 1. Create a permanent run/debug configuration from an executable method or class

- 把插入符放到可执行方法或类的声明上，按`Alt+Enter`，这样，IDEA将创建一个对应类型的永久的run/debug配置文件

#### 2. Save a temporary configuration as permanent

- 在run/debug configuration中选中临时配置文件，点击Save configuration（点击 ![Save](https://resources.jetbrains.com/help/img/idea/2021.1/icons.actions.menu-saveall_dark.svg)）即可。

#### 3. Create a run/debug configuration from a template

- Open the Run/Debug Configuration
  - Select **Run | Edit Configurations** from the main menu.
  - With the [Navigation bar](https://www.jetbrains.com/help/idea/guided-tour-around-the-user-interface.html#navigation-bar) visible (**View | Appearance | Navigation Bar** ), choose **Edit Configurations** from the run/debug configuration selector.
  - Press `Alt+Shift+F10`, then press `0` or select the configuration from the popup and press `F4`.
- In the Run/Debug Configuration dialog, add a configuration template and fill with the necessary information.See [Here](https://www.jetbrains.com/help/idea/run-debug-configuration.html#config-folders) For details.

# 二、详细说明

## 1. Breakpoint 

### Breakpoint分类

The following types of breakpoints are available in IntelliJ IDEA:

- *Line breakpoints*: suspend the program upon reaching the line of code where the breakpoint was set. This type of breakpoints can be set on any executable line of code.
- *Method breakpoints*: suspend the program upon entering or exiting the specified method or one of its implementations, allowing you to check the method's entry/exit conditions.
- *Field watchpoints*: suspend the program when the specified field is read or written to. This allows you to react to interactions with specific instance variables. For example, if at the end of a complicated process you are ending up with an obviously wrong value on one of your fields, setting a field watchpoint may help determine the origin of the fault.
- *Exception breakpoints*: suspend the program when `Throwable` or its subclasses are thrown. They apply globally to the exception condition and do not require a particular source code reference.

### Manage breakpoint

#### Remove breakpoint

- 在装订线中点击可移除
- 打开**Run|View Breakpoints** (`Ctrl+Shift+F8`)，选择要关闭的断点关闭。

#### Mute breakpoints

> 断点静音可以让所有断点在某个阶段全部不生效，之后打开后，依然生效

- Click the **Mute Breakpoints** button ![Mute Breakpoints button](https://resources.jetbrains.com/help/img/idea/2021.1/icons.debugger.muteBreakpoints.svg) in the toolbar of the **Debug** tool window.

#### Enable/disable breakpoints

> 暂时性地关闭某个断点、某部分断点

- 右键点击断点，关闭Enabled即可
- 当然也可以在**View Breakpoints**中暂时性地关闭所有断点/某部分断点。

#### Move/Copy breakpoints

- 拖拽某个断点可移动它
- 按住`Ctrl`后，拖拽某个断点，可复制它，这时候两个断点地参数是相同的

### Configure breakpoints' properties

- 把插入符放到指定行，然后按`Alt+Enter`进入该行的断点进行设置
- 如果要设置列表所有的断点: 右键断点，然后点击**More**（`Ctrl+Shift+F8`）

> 详情请看[这里](https://www.jetbrains.com/help/idea/using-breakpoints.html#breakpoint-properties)

## 2. Start the debugger session

- 点击在靠近类的`main()`方法周围的在装订线的**Run** ![Run icon](https://resources.jetbrains.com/help/img/idea/2021.1/icons.runConfigurations.testState.run_dark.svg) 图标，选择**Debug**，这样就可以创建一个临时的run/debug configuration。
- 如果已经有run/debug configuration，在这里面选择配置文件，然后按`Shift+F9`
- 如果已经有run/debug configuration。而且还未别选择，在debug之前想要换这个配置文件再运行debug，按`Alt+Shift+F9`，之后选择想要的配置文件或继续执行**Edit Configuration**

## 3.  Debug tool window

> Show/hide: **View | Tool Windows | Debug** or `Alt+5`
>
> Configure: **Settings/Preferences | Build, Execution, Deployment | Debugger**

当开启一个Debugger 会话的时候，Debugger工具窗口就会弹出来。通过这个窗口可以控制debugger会话，显示和分析程序数据（框架、线程、变量等等）还有执行多个debugger行为。

## 4. Examine suspended program

当Debug会话启动，Debug窗口出来以后，程序将会正常运行知道遇到以下情况：

- 遇到断点
- 手动暂停程序

之后，程序就暂停了，允许你检查它现在的状态，控制下一步的运行和测试运行时的多种场景。

### 检查框架

框架会对活动的**方法**和**函数**调用做出响应，它存储调用**方法或函数的本地变量、参数**

关于线程的类型看[这里](https://www.jetbrains.com/help/idea/examining-suspended-program.html#status)

> 如果要隐藏来自库的框架，可以点击右上角的 ![Hide Frames from Libraries button](https://resources.jetbrains.com/help/img/idea/2021.1/icons.general.filter_dark.svg)按钮，忽略对类库的方法调用。

众所周知，方法调用后执行都是在栈中，所以框架的信息其实就是栈中的信息，可以复制这些信息岛剪贴板中，只需在框架中右键选择**Copy Stack**

当然还可以导出栈，如果需要一份包含线程状态信息和栈轨迹的报告，只需在框架中右键选择**Export Stack**，然后指定保存路径后保存即可。

### 检查/更新变量

> 注意变量的范围和生命周起，如果变量没有在列表中，可能是因为当前框架中，变量不在该范围内

变量的种类在[这里](https://www.jetbrains.com/help/idea/examining-suspended-program.html#variable-types)

- Pin Field（这个爷还不会）
- 复制变量：右键变量选择复制变量即可，也可复制变量名

- 与剪切板的变量比较：同样也是右键选择**Compare Value with Clipboard**
- 在专用会话中查看变量：右键变量，选择Inspect。
- 以数组形式查看变量
- **设置变量的值**：右键变量，选择**Set Value**，选择变量后按`F2`，输入值后按`Enter`
- 导航到源码：
  - 右键变量，然后按**Jump to Source**`F4`，导航到变量定义的地方
  - 按**Jump to Source**`Shift+F4`，导航到变量类型声明的地方
  - 点击Navigate可导航到战队跟踪元素的方法体
- 检查对象引用：右键变量，选择**Show Referring Objects**

#东西太多了，略！日，写到[这里](https://www.jetbrains.com/help/idea/examining-suspended-program.html#evaluating-expressions)

