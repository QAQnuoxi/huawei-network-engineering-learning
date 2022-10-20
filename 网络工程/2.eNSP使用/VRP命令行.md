# 基础
- [[#3.1 常用功能]]
- [[#3.2 常见文件系统操作命令]]
- [[#3.3 基本配置命令]]
- [[#3.4 使用案例]]
- 


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

|             |              |
|-------|-----------|
|查看当前目录|``<Huangwei>pwd``|
|显示当前目录下的文件信息|``<Huawei>dir``|
|查看文本文件的具体内容|``<Huawei>more``|
|修改用户当前界面的工作目录|``<Huawei>cd``|
|创建新的目录|``<Huawei>mkdir``|
|删除目录|``<Huawei>rmdir``|
|复制文件|``<Huawei>copy``|
|移动文件|``<Huawei>move``|
|重命名文件|``<Huawei>rename``|
|删除文件|``<Huawei>delete``|
|恢复删除的文件|``<Huawei>undelete``|
|彻底删除回收站中的文件|``<Huawei>resetrecycle-bin``|


- VRP基于文件系统来管理设备上的文件和目录。在管理文件和目录时，经常会使用一些基本命令来查询文件或者目录的信息，常用的命令包括pwd，dir [/all]  [ filename | directory ]和more [/binary] filename [ offset ] [ all ]。
	- pwd命令用来显示当前工作目录。
	-  dir [ /all ] [ filename | directory ]命令用来查看当前目录下的文件信息。
	-  more [ /binary ] filename [ offset ] [ all ]命令用来查看文本文件的具体内容。
	- 本例中，在用户视图中使用dir命令，可以查看flash中的文件信息。
- 目录操作常用的命令包括：cd directory，mkdir directory和rmdir directory。
	- cd directory命令用来修改用户当前的工作目录。
	- mkdir directory命令能够创建一个新的目录。目录名称可以包含1-64个字符。
	- rmdir directory命令能够删除文件系统中的目录，此处需要注意的是，只有空目录才能被删除。
	- copy source-filename destination-filename命令可以复制文件。如果目标文件已存在，系统会提示此文件将被替换。目标文件名不能与系统启动文件同名，否则系统将会出现错误提示。
	-  move source-filename destination-filename命令可以用来将文件移动到其他目录下。move命令只适用于在同一储存设备中移动文件。
	- rename old-name new-name命令可以用来对目录或文件进行重命名。
	- delete [ /unreserved ] [ /force ] { filename | devicename }命令可以用来删除文件。不带unreserved参数的情况下，被删除的文件将直接被移动到回收站。回收站中的文件也可以通过执行undelete命令进行恢复，但是如果执行delete命令时指定了unreserved参数，则文件将被永久删除。在删除文件时，系统会提示“是否确定删除文件”，但如果命令中指定了/force 参数，系统将不会给出任何提示信息。filename参数指的是需要删除的文件的名称，devicename参数指定了储存设备的名称。
	- reset recycle-bin [ filename | devicename ]可以用来永久删除回收站中的文件，filename参数指定了需要永久删除的文件的名称，device-name参数指定了储存设备的名称。


### 3.3 基本配置命令
|        |        |     |
|--------|---------|----------|
|1.配置设备名称|``[Huawei] sysname name``|         |
|2.设置系统时钟|``<Huawei> clock timezone time-zone-name { add l minus } offset``|    用来对本地时区信息进行设置。  |
|2.设置系统时钟|``<Huawei> clock datetime [ utc ] HH:MM:S5 YYYY-MM-DD``|用来设置设备当前或UTC日期和时间。|
|2.设置系统时钟|``<Huawei> clock dayliqht-saving-time``|  用来设置设备的夏令时|
|3.配置命令等级|``[Huawei] command-privilege level level view view-name command-key``|用来设置指定视图内的命令的级别。命令级别分为参观、监控、配置、管理4个级别，分别对应标识0、1、2、3。|
|4.配置用户通过Password方式登录设备|``[Huawei]user-interface vty 0 4[Huawei-ui-vty0-4]set authentication password cipher information``|用来进入指定的用户视图并配置用户认证方式为password. 系统支持的用户界面包括Console用户界面和VTY用户界面，Console界自用于本地受录，VTY界自用于远程登录。默认情况下，设备一般最多支持15个时通过VTY方式访问。|
|5.配置用户界面参数|``[Huawei] idle-timeout minutes [ seconds ]``|用来设置用户界面断开连接的超时时间。如用户在一段时间内没有输入命令，系统将断开连接。缺省情况下，超时时间是10分钟。
|6.配置接口IP地址|``[Huawei]interface interface-number   [Huawei-interface-mumbed address ip address``|用来给设备上的物理或逻辑接口配置IP地址。|
|7.查看当前运行的配置文件|``<Huawei>display current-configuration``||
|8.配置文件保存|``<Huawei>save``||
|9.查看保存的配置|``<Huawei>display saved-configuration``| |
|10.清除已保存的配置|``<Huawei>reset saved-configuration``||
|11.查看系统启动配置参数|``<Huawei> display startup``|用来查看设备本次及下次启动相关的系统软件、备份系统软件、配置文件、License文件、补丁文件以及语音文件。|
|12.配置系统下次启动时使用的配置文件|``<Huawei>startup saved-configuration configuration-fle``|设备升级时，可以通过此命令让设备下次启动时加载指定的配置文件|
|13.配置设备重启|``<Huawei>reboot``||



- 网络上一般都会部署不止一台设备，管理员需要对这些设备进行统一管理。在进行设备调试的时候，首要任务是设置设备名。设备名用来唯一地标识一台设备。AR系列路由器默认的设备名是Huawei，而S系列交换机默认的设备名是HUAWEI。设备名称一旦设置，立刻生效。
- 为了保证与其他设备协调工作，需要准确设置系统时钟。系统时钟的=UTC（Coordinated Universal Time）+当前时区与UTC的时间差，一般设备上都会有内置的UTC和时间差配置。
	- 可以通过clock datetime命令直接设置设备的系统时钟，格式为HH:MM:SS YYYY-MM-DD，此时UTC等于系统时钟-时间差。
	- 也可以通过修改UTC和系统当前时区来修改系统时钟
		- ``clock datetime [ utc ] HH:MM:SS YYYY-MM-DD``用来修改UTC时间。
		- clock timezone time-zone-name { add | minus } offset 用来配置本地时区信息。本地时间加上或减去offset即为UTC。
	- 有的地区实行夏令时制，因此当进入夏令时实施区间的一刻，系统时间要根据用户的设定进行夏令时时间的调整。VRP支持夏令时功能。
- 每类用户界面都有对应的用户界面视图。用户界面（User-interface）视图是系统提供的一种命令行视图，用来配置和管理所有工作在异步交互方式下的物理接口和逻辑接口，从而达到统一管理各种用户界面的目的。在连接到设备前，用户要设置用户界面参数。系统支持的用户界面包括Console用户界面和VTY用户界面。控制口（Console Port）是一种通信串行端口，由设备的主控板提供。虚拟类型终端（Virtual Type Terminal）是一种虚拟线路端口，用户通过终端与设备建立Telnet或SSH连接后，也就建立了一条VTY，即用户可以通过VTY方式登录设备。设备一般最多支持15个用户同时通过VTY方式访问。执行user-interface maximum-vty number 命令可以配置同时登录到设备的VTY类型用户界面的最大个数。如果将最大登录用户数设为0，则任何用户都不能通过Telnet或者SSH登录到路由器。display user-interface 命令用来查看用户界面信息。
- 不同的设备，或使用不同版本的VRP软件系统，具体可以被使用的VTY接口的最大数量可能不同。
- 要在接口运行IP服务，必须为接口配置一个IP地址。一个接口一般只需要一个IP地址,如果接口配置了新的主IP地址，那么新的主IP地址就替代了原来的主IP地址。
- 用户可以利用ip address ip-address { mask | mask-length } 命令为接口配置IP地址，这个命令中，mask代表子网掩码，如255.255.255.0，mask-length 代表的是掩码长度，如24。这两者任取其一均可。
- Loopback接口是一个逻辑接口，可用来虚拟一个网络或者一个IP主机。在运行多种协议的时候，由于Loopback接口稳定可靠，所以也可以用来做管理接口。
- 在给物理接口配置IP地址时，需要关注该接口的物理状态。默认情况下，华为路由器和交换机的接口状态为up；如果该接口曾被手动关闭，则在配置完IP地址后，应使用undo shutdown打开该接口。
- reset saved-configuration命令用来清除配置文件或配置文件中的内容。执行该命令后，如果不使用命令startup saved-configuration重新指定设备下次启动时使用的配置文件，也不使用save命令保存当前配置，则设备下次启动时会采用缺省的配置参数进行初始化。
- display startup命令用来查看设备本次及下次启动相关的系统软件、备份系统软件、配置文件、License文件、补丁文件以及语音文件。
- startup saved-configuration configuration-file 命令用来指定系统下次启动时使用的配置文件，configuration-file参数为系统启动配置文件的名称。
- reboot命令用来重启设备，重启前提示用户是否保存配置。

### 3.4 使用案例
1. 文件查询命令、目录操作
- 需求：
	- 查看路由器RTA当前目录下的文件和目录的信息;
	- 创建一个新目录test，然后删除该目录。

```
<Huawei>pwd
flash:
<Huawei>dir
Directory of flash:/

  Idx  Attr     Size(Byte)  Date        Time(LMT)  FileName 
    0  drw-              -  Oct 20 2022 06:55:35   dhcp
    1  -rw-        121,802  May 26 2014 09:20:58   portalpage.zip
    2  -rw-          2,263  Oct 20 2022 06:55:31   statemach.efs
    3  -rw-        828,482  May 26 2014 09:20:58   sslvpn.zip

1,090,732 KB total (784,464 KB free)
<Huawei>mkdir test
Info: Create directory flash:/test......Done
<Huawei>dir
Directory of flash:/

  Idx  Attr     Size(Byte)  Date        Time(LMT)  FileName 
    0  drw-              -  Oct 20 2022 06:56:14   test
    1  drw-              -  Oct 20 2022 06:55:35   dhcp
    2  -rw-        121,802  May 26 2014 09:20:58   portalpage.zip
    3  -rw-          2,263  Oct 20 2022 06:55:31   statemach.efs
    4  -rw-        828,482  May 26 2014 09:20:58   sslvpn.zip

1,090,732 KB total (784,460 KB free)
<Huawei>rmdir test
Remove directory flash:/test? (y/n)[n]:y
%Removing directory flash:/test...Done!
<Huawei>dir
Directory of flash:/

  Idx  Attr     Size(Byte)  Date        Time(LMT)  FileName 
    0  drw-              -  Oct 20 2022 06:55:35   dhcp
    1  -rw-        121,802  May 26 2014 09:20:58   portalpage.zip
    2  -rw-          2,263  Oct 20 2022 06:55:31   statemach.efs
    3  -rw-        828,482  May 26 2014 09:20:58   sslvpn.zip

1,090,732 KB total (784,464 KB free)
<Huawei>
```

-----

2. 文件操作(一)
- 需求说明:
	- 将文件huawei.txt重命名为save.zip；
	- 将文件save.zip复制并命名为file.txt；
	- 将文件file.txt移动到dhcp目录下;
	- 删除文件file.txt；
	- 恢复删除文件file.txt。
- ![[Pasted image 20221020152619.png]]
- ![[Pasted image 20221020152927.png]]
-------

3. VRP基本配置命令
![[Pasted image 20221020153111.png]]
如图，某工程师需要为公司配置路由器，需求如下:
- 路由器与PC互通，地址规划如图;
- 公司其他人员可以通过PC远程登录访问路由器，密码是huawei123，但是只能查看配置不能随意修改配置命令;
- 将当前配置保存为huawei.zip文件，并配置系统下次启动时使用该配置文件。



> 配置接口地址
```
<Huawei>system-view
Enter system view, return user view with Ctrl+Z.
[Huawei]sysname AR1
[AR1]interface GigabitEthernet 0/0/1
[AR1-GigabitEthernet0/0/1]ip address 192.168.1.1 24
[AR1-GigabitEthernet0/0/1]quit

```

>配置用户权限和用户认证

```
[AR1]user-interface vty 0 4
[AR1-ui-vty0-4]authentication-mode password
Please configure the login password (maximum length 16):huawei123
[AR1-ui-vty0-4]user privilege level 1
[AR1-ui-vty0-4]quit
[AR1]

```
附：
- 部分型号设备配置密码时只需要输入” authentication-mode password “命令，便会自动跳出输入密码的页面，将密码输入即可；
-  有的设备密码设置命令为” set authentication-mode password 密码”，密码需要手动输入。


>配置系统下次启动文件

![[Pasted image 20221020155202.png]]

- 设备中直接使用命令”save”进行保存，默认保存在vrpcfg.cfg文件中，当然也可以更改保存文件名,VRP5操作系统默认文件放置在flash:目录下

> 查看配置结果

![[Pasted image 20221020155316.png]]


- display startup命令用来查看设备本次及下次启动相关的系统软件、备份系统软件、配置文件、License文件、补丁文件以及语音文件。
- Startup system software表示的是本次系统启动所使用的VRP文件。
- Next startup system software表示的是下次系统启动所使用的VRP文件。
- Startup saved-configuration file表示的是本次系统启动所使用的配置文件。
- Next startup saved-configuration file表示的是下次系统启动所使用的配置文件。
- 设备启动时，会从存储设备中加载配置文件并进行初始化。如果存储设备中没有配置文件，设备将会使用默认参数进行初始化。
- startup saved-configuration [configuration-file] 命令用来指定系统下次启动时使用的配置文件，configuration-file参数为系统启动配置文件的名称

### 3.5 附

![[Pasted image 20221020155513.png]]

 >思考：
1. 华为数通设备目前使用的VRP版本是多少？
2. 华为网络设备支持多少个用户同时使用Console口登录?
3. 如果设备中有多个配置文件，如何指定下次启动时使用的配置文件?

1. 目前，大多数华为数通产品使用的是VRP5版本，少数产品如NE系列路由器使用的是VRP8版本。
2. 华为网络设备同时只能有一个用户登录Console界面，因此Console用户的编号固定为0。
3. 需要指定某一配置文件为下次启动时使用的配置文件，可以执行startup saved-configuration configuration-file 命令，这里的配置文件名包括文件名称和扩展名。