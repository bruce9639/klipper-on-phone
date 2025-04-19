# klipper-on-android

**在android系统上运行klipper，moonraker，fluidd，KlipperScreen，一键脚本快速配置。**

**Run klipper, moonraker, fluidd, KlipperScreen on android system, one-click script for quick configuration.**


**感谢以下教程：**

B站：

[【保姆级教程】告别树莓派，安卓klipper全新安装方案+一键配置脚本，只需一点，从此告别安卓手机只能换脸盆的命运](https://www.bilibili.com/video/BV1BG4y1t7RR/?vd_source=7fb5d23ecaaec9c060d89fdde67b3d1b)



***站在巨人的肩膀上！！！***


***感谢:***

***klipper, moonraker 和 xterm 初始化脚本来自 @d4rk50ul1***

***(https://github.com/d4rk50ul1/klipper-on-android)***

***ttyACM0 初始化脚本来自 @CODERUS***

***(https://gist.github.com/CODERUS/a5ec4a456f5b58186cbebb66a8542a2e)***

***原教程来自 @gaifeng8864***

***([(https://github.com/gaifeng8864/klipper-on-android)](https://github.com/gaifeng8864/klipper-on-android))***


**前言：**

**特别说明：**

本教程里的安装方案使用的安卓系统最好选择安卓5或以上版本，低于安卓5系统需要使用低版本linuxdeploy。高于安卓9系统可能会有权限相关的兼容性问题，但是并不都这样，仍然值得一试。另外，如果遇到openssh自动启动失败（表现为无法使用ssh连接debian系统），这种情况一般是安卓内核与linuxdeploy兼容问题，需要更换不同内核版本的安卓系统（注意，是不同内核版本）。推荐使用AOSP等类原生安卓系统。

0.本教程虽尽量做到步骤全面和详细，但是因为涉及基本的linux系统使用和klipper配置，所以并不适合完全小白用户。不过，都已经准备使用安卓手机运行klipper了，相信这些已经不是问题。

1.本教程中安卓系统必须root。因每种手机硬件和系统的root方法各有不同，网络上教程很多，这里不再赘述。

2.本教程示例软硬件环境：

  手机系统必须获取root权限
		
3.理论上，只要能root的安卓手机此教程都能适用，待测试。	
	  
4.理论上，在termux里安装proot容器后安装的debian系统也可以使用，待测试。

5.下位机已提前烧录klipper固件。
	  
	  
**本教程特点:**

1.使用一个单独的APP在图形界面下安装linux系统，配置系统自动重启，系统中文汉化等。方便管理和使用。

2.klipper系统依旧使用流行的kiauh脚本进行安装，升级或卸载。手机KlipperScreen界面或者网页界面都可以进行升级操作。

3.使用一键脚本对klipper全家桶进行快速配置，方便快捷。
			
4.结合我自己的使用经验，修改定制了klipper全家桶的基本配置文件，一些关键配置更加实用。
           								
5.安装好以后无任何报错，可单独控制klipper，moonraker等服务启动，重启，关闭。
			
6.使用体验与树莓派基本没有区别，包括使用输入整形与压力补偿，手机CPU温度显示，打印机主板固件SD卡在线升级等。
			
#######################################################################################################			  


***安装过程较长，手机需要连接到充电器！！！！！！***

***本教程假设：***

***安卓手机已root ！！！！！！***

***主板内SD卡中已烧录klipper固件：[klipper(MKS SGEN-L V1.0).bin](https://github.com/gaifeng8864/klipper-on-android/blob/main/klipper(MKS%20SGEN-L%20V1.0).bin?raw=true)***

***（SD卡中的klipper固件需要根据主板型号进行编译，此链接中的固件只适用于MKS SGEN-L V1.0）***


需要用的软件：

手机端：

XServer-XSDL-1.20.51.apk（必装） 下载链接：https://sourceforge.net/projects/libsdl-android/files/apk/XServer-XSDL/
    
linuxdeploy-2.6.0-259.apk（必装） 下载链接：https://github.com/meefik/linuxdeploy/releases/download/2.6.0/linuxdeploy-2.6.0-259.apk

kerneladiutor_248.apk（开启所有CPU核心。如果linuxdeploy里的Debian系统无法识别到所有的CPU核心，推荐安装） 下载链接：https://f-droid.org/en/packages/com.nhellfire.kerneladiutor/

termux_118.apk（选装，需要时再安装）下载链接：https://github.com/termux/termux-app/releases/tag/v0.118.0
		
电脑端：

ssh软件有很多，选一个自己喜欢的


## 0.安装XServer-XSDL ##

XServer-XSDL安装比较简单，按系统提示直接下一步就可以了。

注意：安装完成后需要在第一次启动的界面点击屏幕上方 “更改设备设置” 按钮进入设置界面，依次点击 “鼠标模拟”---“鼠标仿真模式”---“桌面版，无仿真”，然后下拉到最底下点击“完成”。否则触摸无法使用。

如果错过了第一次启动的界面，关闭XServer-XSDL后台运行后再次启动XServer-XSDL即可。

## 1.安装kerneladiutor ##

高通处理器默认有个MPD功耗控制方案，默认情况下会关闭部分CPU核心来控制功耗。
由此带来的最大的问题就是在debian系统里会发现4核心的处理器大多数情况下却只识别出2个核心。
kerneladiutor是简单好用的安卓系统的内核管理软件，用来调整CPU和GPU的频率和性能。可以强制开启所有CPU核心，充分利用手机的性能。
经实际测试发现，较新的安卓手机已可以被linuxdeploy里安装的debian自动识别到所有的CPU核心。所以，推荐初始不安装，如果需要再安装。

## 2.安装linuxdeploy ##

根据系统提示安装apk文件，安装完成后打开软件点击主界面左上角“![三横杠](https://user-images.githubusercontent.com/16047447/201450191-fc8d09bc-7ae5-4e9a-97a2-a92c5fdf36c2.PNG)
”打开软件设置：

	屏幕常亮  （不勾选）
	
	锁定Wi-Fi （勾选）

	CPU唤醒   （勾选）

	开机自启动 （勾选）

	语言      （简体中文）

	启动延迟   （5）  （以秒为单位，根据需要设置，想迟一点启动就将数字调大）

	联网更新   （勾选）

其他选项保持默认！！！！！！！！！！

！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！


## 3.安装debian系统 ##

设置好后回到主界面，点击右下角“![滑动开关](https://user-images.githubusercontent.com/16047447/201450293-096d3977-1b77-435d-8106-bf95c27d6052.PNG)
”打开linux系统安装配置。

	发行版 （Debian）

	架构（armhf）（自行查询自己手机处理器型号，如果支持64位就选arm64）

	发行版本 （oldstable）

	源地址 （https://mirrors.aliyun.com/debian/或者https://mirrors.cloud.tencent.com/debian/ ）

	安装类型 （目录）

	安装路径 （/data/debian11）

	用户名 （随便取）后面要ssh新建klipper用户

	密码：（123456）（随便设置，能记住就行）

	初始化 （启用）

	初始化系统 （sysV）

	挂载点 （可选，此应用场景下一般用不到）

	挂载点列表 （/sdcard/-/mnt/sdcard/）（可选，此应用场景下一般用不到）

	SSH （启用）（必须开启，否则连不上linux系统）

	图形界面 （启用）

	图形子系统 （X11）

	图形界面设置 （取消勾选自动打开XServer-XSDL）（一键配置脚本里已经配置自动开启XServer-XSDL客户端，此处不需要勾选）

	桌面环境 （XTerm）

其他选项保持默认！！！！！！！！！！

！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！

配置完成后回到主界面

点击右上角“![三竖点](https://user-images.githubusercontent.com/16047447/201450321-113c378f-88ec-4009-871f-0c76eabf3004.PNG)
” 点击 “安装” （安装过程比较漫长，根据网络情况大概在5-15分钟）

界面下方出现 "<<< deploy" 时说明安装完成

点击主界面下方“停止”，出现"<<< stop" 再点击“启动”（这一步不能少，否则debian系统启动不了）

系统启动完成后使用Xshell连接debian系统（IP地址显示在linuxdeploy软件主界面最上方，登录名和密码就是上面步骤里设置的，端口22，协议ssh）

至此，debian系统安装完成！！！！！！！！！！！！！！！！

！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！


## 4.klipper安装环境配置 ##

ssh登录进入debian系统后执行以下命令：

	sudo usermod -a -G aid_inet,aid_net_raw root

###由于安卓系统上chroot容器权限问题，除初始登录用户外，默认其他用户没有网络权限，包括root用户。此命令可以解决使用sudo命令时root用户无法联网的问题。



	sudo apt update

###更新系统软件包



	sudo apt install -y git wget
	
	sudo apt install usbutils libusb-1.0-0-dev  #usb依赖
 
 #修改root密码	
	
        sudo passwd

 #切换root用户 	
	
         su root 

#新建klipper用户并设置密码##
	
        adduser klipper 

 #如果出现出现“不在sudoers文件中，此事将被报告”的问题先切换至root用户#
         
	 su root

#设置 /etc/sudoers 文件权限，添加 可写权限,输入修改权限命令

      chmod u+w /etc/sudoers

#执行nano命令，编辑/etc/sudoers文件
      
      nano /etc/sudoers

#找到root ALL=(ALL) ALL 的下一行添加代码：
 
     klipper ALL=(ALL) ALL     

#ctrl+x保存退出    

#进入klipper用户	#
	
         su klipper
	
## 5.使用kiauh安装klipper ##

	cd ~

###进入登录用户家目录



	git clone https://gitee.com/miroky/kiauh.git

###国内kiauh脚本地址（与上面官方地址二选一即可）



	./kiauh/kiauh.sh
	
###启动脚本开始安装klipper全家桶



###共需要安装klipper，moonraker，fluidd（一键脚本暂时不支持Mainsail配置），KlipperScreen 这4个组件。
每安装完一个组件都会提示无法启动服务，这是安卓初始化系统与klipper全家桶服务启动方式不兼容的原因，不用管它，如果能启动起来就不用一键脚本去配置了。
组件安装涉及部分编译过程，耗时较长，耐心等待。只要是每个脚本都能自动安装到最后，基本就没有问题。



## 6.klipper全家桶配置 ##

使用Xftp或命令行进入以下路径：

	/home/klipper/printer_data/config/

将里面的 fluidd.cfg,moonraker.conf,printer.cfg 进行备份之后把原文件删除。

	cd ~

###进入登录用户家目录

将本仓库里的fluidd.cfg,homing_override.cfg,moonraker.conf,printer.cfg 这4个文件下载后放到 /home/klipper/printer_data/config/ 路径下。



***！！！！！！将打印机主板上电启动，使用 OTG线将手机和打印机主板连接！！！！！！***

运行以下命令，查找打印机主板在安卓手机里被识别到的串口设备路径与所在的用户组：

	ls -al /dev/


输出信息一般像这样：

	crw-rw----.  1 aid_radio     aid_radio        166,   0  6月 29 11:57 ttyACM0


在以上的输出信息中，第二个 aid_radio 就是串口设备所在的用户组，ttyACM0 就是串口设备的名称。完整的串口设备路径就是 /dev/ttyACM0

***注意！！！不同的打印机主板在不同的安卓手机里的名称和用户组可能是有区别的，请注意仔细确认！！！***

***如果运行命令 ls -al /dev/ 之后输出的信息类似以下这样：***

	crw-rw----.  1 root     root        166,   0  6月 29 11:57 ttyACM0

***这意味着 ttyACM0 归属于 root 用户组，步骤6中接下来的操作将不再适用，请直接跳转到步骤  7.如果 ttyACM0 归属于 root 用户组的处理方式***


如果确认 ttyACM0 归属于非root用户组，运行以下命令将用户 print3D 添加到串口设备所在的用户组里，使得用户 print3D 获得对串口设备的读写权限。


 	sudo usermod -a -G aid_radio print3D


继续执行如下命令：

 	cd ~

###进入登录用户家目录

	sudo wget https://raw.githubusercontent.com/gaifeng8864/klipper-on-android/main/configuration_klipper_family.sh

	bash configuration_klipper_family.sh
 

 ***注意！！！*** 如果你的串口设备路径不是默认的 /dev/ttyACM0

请执行以下命令： 

	bash configuration_klipper_family.sh -p "识别到的串口设备路径"
 

执行完毕后重启手机，没有问题的话klipper全家桶和XServer-XSDL会自动启动并连接到打印机，屏幕上会显示KlipperScreen经典界面。


## 7.如果 ttyACM0 归属于 root 用户组的处理方式 ##

执行如下命令：

 	cd ~

###进入登录用户家目录

	sudo wget https://raw.githubusercontent.com/gaifeng8864/klipper-on-android/main/configuration_klipper_family_V1.0.sh

	bash configuration_klipper_family_V1.0.sh
 

 ***注意！！！*** 如果你的串口设备路径不是默认的 /dev/ttyACM0

请执行以下命令： 

	bash configuration_klipper_family_V1.0.sh -p "识别到的串口设备路径"
 

 执行完毕后重启手机，没有问题的话klipper全家桶和XServer-XSDL会自动启动并连接到打印机，屏幕上会显示KlipperScreen经典界面。




***小技巧!!!***

如果不好确认串口设备的名称，可以使用以下方法：

1.将打印机主板与安卓手机断开连接，重启手机，进入debian内，运行以下命令并保存输出结果：

	ls /dev/

2.将打印机主板与安卓手机连接，重启手机，进入debian内，运行以下命令并保存输出结果：

	ls /dev/

3.将步骤1和步骤2的结果进行比较，可以很容易地确认设备是否被正确识别以及设备名称。




