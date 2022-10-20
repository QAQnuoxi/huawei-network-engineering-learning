# 基础


## 1.基本命令结构
- 华为提供的命令按照一定的格式设计，用户可以通过命令行界面输入命令，由命令行界面对命令进行解析，实现用户对路由器的配置和管理
	- 命令字：规定了系统应该执行的功能，如
		- display(查询设备状态)
		- reboot（重启设备)
		- 。。。。。。
	- 关键字：特殊的字符构成，用于进一步约束命令，是对命令的拓展，也可用于表达命令构成逻辑而增设的补充字符串。
	- 参数列表：是对命令执行功能的进一步约束。包括一对或多对参数名和参数值。
![[Pasted image 20221020094429.png]]

- 举例一：
``` vrp
display ip interface GE0/0/0,查看接口信息的命令
命令字：display
关键字：ip
参数名：interface
参数值：GE0/0/0
```

- 举例二：
```
Reboot,重启设备的命令
命令字：reboot,操作命令都要用命令字，并且必
须从规范的命令字集合中选取。
```

- 规范：
	- 每条命令有最多一个命令字，若干个关键字和参数，形成一条命令，参数必须由参数名和参数值组成。
	- 命令字、关键字、参数名、参数值之间，需要用空格分隔开。

## 2.命令行视图

- 设备提供了多样的配置和查询命令，为便于用户使用这些命令，VRP系统按功能分类将命令分别注册在不同的命令行视图下。
	- 用户视图：用户可以完成查看运行状态和统计信息等功能。
	- 系统视图：用户可以配置系统参数以及通过该视图进入其他的功能配置视图。
	- 其他视图：比如接口视图，协议视图，用户可以进行接口参数和协议参数配置。
![[Pasted image 20221020100027.png]]
> 说明
- 用户视图应为登录系统后的第一个视图。在用户视图中不提供除查询和工具命令之外的其他命令。
- 用户视图中，唯一可进入的视图是系统视图；系统视图中提供全局的配置命令；如果系统存在下一级配置视图，则在系统视图中须提供进入下一级配置视图的命令。
- 登录到系统后，首先进入的是用户视图，这里仅提供查询，以及ping，telnet等工具命令，不提供任何配置命令。
- 用户视图下通过system-view命令可以进入到系统视图，系统视图提供一些简单的全局配置功能。
- 一些复杂的配置功能，如配置一个以太网接口，需要的参数比较多，因此系统提供以太网接口的配置视图，在系统视图中，使用命令interface GigabitEthernet X（此处X表示一个具体接口的编号） 进入GE接口配置视图，这个配置视图当前针对GE接口X，所有的命令仅对GE接口X生效。




![[命令行视图说明.png]]

>命令行举例：
```
<Huawei>system-view         #用户首先进入用户视图，通过命令进入系统视图
[Huawei]interface GigabitEthernet 0/0/1       #在系统视图进入接口视图
[Huawei-GigabitEtherneto/0/1]ip address 192.168.1.1 24            #配置1P地址
[Huawei-GigabitEtherneto/0/1]quit         #退回到上一个视图
[Huawei]ospf 1         #在系统视图进入协议视图
[Huawei-ospf-1]area 0         #在协议视图进入OSPF区域视图
[Huawei-ospf-1-area-0.0.0.0]return            #返回用户视图
```

## 3. 命令行编辑
### 3.1 常用功能
#### 功能键
-  退格键Backspace: 删除光标位置的前一个字符，光标左移，若已经到达命令首，则响铃告警
- 左光标键 <--  或<Ct+B>: 光标向左移动一个字符位置，若已经到达命令首，则响铃告警。
- 右光标键 --> 或<Ctl+F>: 光标向右移动一个字符位置，若已经到达命令尾，则响铃告警。
#### 不完整关键字输入
设备支持不完整关键字输入，即在当前视图下，当输入的字符能够匹配唯一的关键字时，可以不必输入整的关键字。
例如：
```
<Huawei>d cu
<Huawei>di cu
<Huawei>dis cu
<Huawei>d c
	^
Error:Ambiguous command found at'^'position.
<Huawei>dis c
      ^
Error:Ambiguous command found at'^'position.
```

- "display current-configuration"命令，可以输入dcu、dicu或dis cu等都可以执行此命令，但不能输入dc disc等，因为以dc、disc开头的命令不唯一。
- 注：此处的关键字与命令行格式中的“关键字”不同，一条命令中除“参数值”外都可以被叫做关键字。
#### Tab键的使用


- 如果与之匹配的关键字唯一，按下``<Tab>``键，系统自动补全关键字，不全后，反复按``<Tab>``关键字不变。
```
[Huawei] info-   #按下Tab键
[Huawei] info-center
```
- 如果与之匹配的关键字不唯一，反复按``<Tab>``键可循环显示所有以输入字符串开头的关键字
```
[Huawei] info-center log   #按下Tab键
[Huawei] info-center logbuffer    #继续按Tab键循环翻词
[Huawei] info-center logfile
[Huawei] info-center loghost
```
-  如果没有与之匹配的关键字，按Tab键后，关键字不变。
```
[Huawei] info-center loglog   #输入错误的关键字，按下Tab键
[Huawei] info-center loglog
```

#### 使用命令行在线帮助
- 用户在使用命令行时，可以使用在线帮助以获取实时帮助，从而无需记忆大量的复杂的命令。
- 命令行在线帮助可分为完全帮助和部分帮助，可通过输入“？”实现。
	- 完全帮助 输入“？”
	- 部分帮助输入“ `开头字符`？”
	- 当用户输入命令时，如果只记得此命令关键字的开头一个或几个字符，可以使用命令行的部分帮助获取以该字符串开头的所有关键字的提示。
#### 解读命令行的错误信息

- 用户键入的命令，如果通过语法检查，则正确执行，否则系统将会向用户报告错误信息。
```
[Huawei] sysname
				 ^
Error:Incomplete command found at '^' position. #箭头所指地方提示命令不完整，需要进一步补齐

[Huawei] router if 1.1.1.1
				 ^
Error: Unrecognized command found at '^' position. #箭头所指地方提示该命令不能识别，需要确认命令正确性
[Huawei] a
		 ^
ErrorAmbiguous command found at '^' position. #箭头所指的命令不明确，有多个a开头的关键字

[Huawei-GigabitEthernetO/O/O]ospf cost 800000 #箭头所指的参数值越界
									   ^	
Error: Wrong parameter found at '^' position.
```

#### 使用undo命令行
在命令前加undo关键字，即为undo命令行。undo命令行一般用来恢复缺省情况、禁用某个功能或者删除某项配置。以下为参考案例:

1. 使用undo命令行恢复缺省情况
```
<Huawei> system-view
[Huawei] sysname Server
[5erver] undo sysname
[Huawei]
```

2. 使用undo 禁用某个功能
```
<Huawei> system-view
[Huawei] ftp server enable
[Huawei] undo ftp server
```

3. 使用undo 命令删除某项设置
```
[Huawei]interface g0/0/1
[Huawei-GigabitEtherneto/0/1]ip address 192.168.1.1 24
[Huawei-GigabitEtherneto/0/1]undo ip address
```

####  使用命令行的快捷键
- 用户可以使用设备中的快捷键，完成对命令的快速输入，从而简化操作。
- 系统中的快捷键分成两类
	- 自定义快捷键
		- <Ctrl+G>
		- <Ctrl+L>
		- <Ctrl+O>
		- <Ctrl+U>
		- 用户可以根据自己的需要将这4个快捷键与任意命令进行关联，当使用快捷键时，系统自动执它所对应的命令。
	- 系统快捷键
		- CTRL_A：将光标移动到当前行的开头
		- CTRL_B: 将光标向左移动一个字符
		- CTRL_C: 停止当前命令的运行
		- CTRL_E: 将光标移动到当前行的末尾
		- CTRL_X: 删除光标左侧所有的字符
		- CTRL_Y: 删除光标所在位置及其右侧所有的字符
		- CTRL_Z: 返回到用户视图
		- CTRL+]：终止当前连接或切换连接



### 3.2 常见文件系统操作命令





- VRP基于文件系统来管理设备上的文件和目录。在管理文件和目录时，经常会使用一些基本命令来查询文件或者目录的信息，常用的命令包括pwd，dir [/all]  [ filename | directory ]和more [/binary] filename [ offset ] [ all ]。
	- pwd命令用来显示当前工作目录。
	-  dir [ /all ] [ filename | directory ]命令用来查看当前目录下的文件信息。
	-  more [ /binary ] filename [ offset ] [ all ]命令用来查看文本文件的具体内容。
	- 本例中，在用户视图中使用dir命令，可以查看flash中的文件信息。
- 目录操作常用的命令包括：cd directory，mkdir directory和rmdir directory。
	- cd directory命令用来修改用户当前的工作目录。
	- mkdir directory命令能够创建一个新的目录。目录名称可以包含1-64个字符。
	- rmdir directory命令能够删除文件系统中的目录，此处需要注意的是，只有空目录才能被删除。


