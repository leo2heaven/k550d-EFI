#### 战神k550d-i7d1
+ 机型：Hasee QJW4 k550d-i7d1
+ 处理器：Intel Corel i7-4710MQ 2.50GHz（最高睿频：3.50GHz）
+ 核显：Intel（R）HD Graphics 4600（1GB）
+ 独显：<s>NIVIDA Geforce GTX 950M</s>--------黑苹果笔记本独显无解
+ 内存：12GB DDR3  800MHz
    + Cruial CT102464BF160B.C16 8GB
    + Samsung M471B5173DH0-YK0 4GB
+ 声卡：Realtek ALC282
+ 无线网卡：
    + 因为想驱动蓝牙使用Airdrop跟随航Handoff所以更换了白果卡，现在好像可以使用Intel Wifi驱动英特尔网卡，但是不能使用Airdrop等
    + <s>Intel AC3160</s>--------DW1550 BCM94352HMB
+ 有线网卡：Realtek RTL8111G
+ 硬盘：使用M.2固态硬盘盒240GB
    + 固态：西部数据 WD GREEN SATA SSD M.2 2280 240GB
    + 硬盘盒：金胜 KINGSHARE KS-ANTU28

## 配置驱动

EFI/CLOVER文件夹下：
| 配置文件     | 说明                                                                                                |
| ------------ | --------------------------------------------------------------------------------------------------- |
| config.plist | 引导参数文件，将适合自己电脑的配置文件改成config.plist,我这里将HD4600config.plist改成了config.plist |

EFI/CLOVER/KEXT文件夹下：
| 驱动文件                             | 说明                                                                                    |
| ------------------------------------ | --------------------------------------------------------------------------------------- |
| ACPIBatteryManager.kext              | 用于黑苹果笔记本电脑电池的驱动,用于实现电池电量显示                                     |
| AirportBrcmFixup.kext                | 非苹果Airport Broadcom/博通 Wi-Fi卡所需的补丁                                           |
| AppleALC.kext                        | 声卡驱动，这里需要在config.plist注入id33                                                |
| AppleCodecCommander.kext             | 可以解决耳机有杂音和睡眠唤醒无法自动切换或无声的问题                                    |
| ApplePS2SmartTouchPad.kext           | 触摸屏驱动，但是加入后，会自己打开打开小键盘，需要修改                                  |
| BrcmBluetoothInjector.kext           | 蓝牙驱动                                                                                |
| BrcmFirmwareData.kext                | 蓝牙驱动                                                                                |
| BrcmPatchRAM3.kext                   | 蓝牙驱动                                                                                |
| BroadcomWiFiInjector.kext            | 博通的WiFi驱动,10.14前免驱，10.15跟big sur都需要添加才能驱动蓝牙                        |
| BT4LEContiunityFixup.kext            | 为IOBluetoothFamily提供补丁                                                             |
| FakePCIID_Broadcom_WiFi.kext         | 黑苹果博通的WiFi仿冒                                                                    |
| FakePCIID_Intel_HD_Graphics.kext     | 黑苹果英特尔显卡仿冒                                                                    |
| FakePCIID_Intel_HDMI_Audio.kext      | 黑苹果hdmi声卡仿冒                                                                      |
| FakePCIID_XHCIMux.kext               | 黑苹果定制USB3.0仿冒                                                                    |
| FakePCIID.kext                       | 黑苹果PCI设备ID仿冒                                                                     |
| FakeSMC_ACPISensors.kext             | 完善传感器信息                                                                          |
| FakeSMC_CPUSensors.kext              | 完善CPU传感器信息                                                                       |
| FakeSMC_GPUSensors.kext              | 完善GPU传感器信息                                                                       |
| FakeSMC_LPCSensors.kext              | 完善传感器驱动                                                                          |
| FakeSMC.kext                         | 仿冒SMC设备                                                                             |
| Lilu.kext                            | 驱动底层依赖                                                                            |
| RealtekRTL8111.kext                  | 有线网卡RTL8111驱动                                                                     |
| VoodooPS2Controller-R6Bronxteck.kext | 键盘驱动，VoodooPS2Controller-.kext太老了，我的电脑一用上，输个密码的时间，键盘就失灵了 |
| WhateverGreen.kext                   | 显卡驱动                                                                                |


+ 黑苹果需要用到ApplePS2SmartTouchPad.kext或者ApplePS2Keyboard.kext但是键盘没有小键盘，每次开机都需要fn+NumLK关闭小键盘的：
如果是ApplePS2SmartTouchPad.kext驱动，修改/Users/nqldy/Desktop/ApplePS2SmartTouchPad.kext/Contents/PlugIns/ApplePS2Keyboard.kext/Contents/Info.plist文件。如果是ApplePS2Keyboard.kext，修改ApplePS2Keyboard.kext/Contents/Info.plist文件。
修改Info.plist的节点IOKitPersonalities/ApplePS2Keyboard/Preferences/Num Lock enabled at boot
默认是`true`，现在修改为`false`，表示开机不启动小键盘

+ 声卡驱动需要注入ID`33`

+ 10.15.X以及11beta，BCM94352HMB的驱动必须要有AirportBrcmFixup.kext、BrcmBluetoothInjector.kext、BrcmFirmwareData.kext、BrcmPatchRAM3.kext和BroadcomWiFiInjector.kext才可以驱动蓝牙以及使用airport以及hondoff

+ 开机会有卡顿一会，是因为`显卡connector`对无效接口没有屏蔽（来自[Xjn819](https://blog.xjn819.com/)大佬），这里我通过修改机型来修复，选择没有独立显卡的机型（`MacBookPro14,1`），然后通过添加引导参数`agdpmod=vit9696`来激活hdmi接口。（改机型可以实现对一些接口的屏蔽但不是正确的解决方法）

+ 打开软件花屏：编辑config.plist -> Properties -> PciRoot(0x0)/Pci(0x2,0x0)

| 项目                  | 类型 | 值       | 备注                                         |
| --------------------- | ---- | -------- | -------------------------------------------- |
| framebuffer-cursormem | data | 00009000 | 如果部分软件花屏，增加这个选项，否则可以不加 |

+ 显存`1076MB`更改显存为`2048MB`
+ 内存频率由`900Mhz`更改为`1067Mhz`

## 致谢
1.致谢 [远景论坛][PCBETA] 的 star__light的[开机启动小键盘解决方案](http://bbs.pcbeta.com/viewthread-1661878-1-1.html)
2.致谢 [黑果小兵](https://blog.daliansky.net)
3.致谢 [远景论坛][PCBETA] 的 flygun2005的[打开软件花屏解决方案](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1824015)
4.致谢 [远景论坛][PCBETA] 的 赵小赵2045的[BCM94352HMB的驱动](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1863711&highlight=94352)
5.致谢贴吧大佬的`肥秋没钱买面包`的[10.14.5驱动](http://tieba.baidu.com/p/4735008414?share=9105&fr=share&unique=6820BAC14ADEE24CD99E8C579D04F44D&st=1596425284&client_type=1&client_version=11.6.1&sfc=copy)
6.致谢[针针](https://hyejeong.cn/)的帮助

[PCBETA]: http://bbs.pcbeta.com/ " 远景论坛"

## 情况
1.在安装的过程中会出现重启四次的情况，如果关机的时候不能完全关机，请手动长按电源键。
2.命令行更改安装全部来源的文件：`sudo spctl --master-disable`
3.如果在安装旧版本的系统显示镜像损坏，其实是因为10.14和10.13会出现安装副本资源已过期，需要断网并且命令行修改日期：`date 0201010116`
4.激活imessage以及facetime打开hackintool的序列号生成器获取主板序列号以及SmUUID，然后在config.plist中的机型修改里面修改。需要注意的是，先查看你的序列号是否已经激活，以及序列号是否对应你的机型。如果没有激活，就可以使用序列号激活。

## 更新
1.2020.7.22 OTA升级10.15.6没有问题
2.2020.8.7 修复usb3.0驱动，向下兼容2.0,usb速度正常
3.2020.8.7 频率修正1067->1600
4.2020.8.8 电源显示正常

## 已知问题
<s>usb定制没有成功，插上u盘，hackintool的usb显示没有反应，全部走的是usb2.0</s>

[11111(https://image.leevast.cn/leevastcom/2/13.png)]
<a href="https://image.leevast.cn/leevastcom/2/13.png"><img src="https://image.leevast.cn/leevastcom/2/13.png"/></a>
<a href="https://image.leevast.cn/leevastcom/2/14.png"><img src="https://image.leevast.cn/leevastcom/2/14.png"/></a>
<a href="https://image.leevast.cn/leevastcom/2/15.png"><img src="https://image.leevast.cn/leevastcom/2/15.png"/></a>
<a href="https://image.leevast.cn/leevastcom/2/16.png"><img src="https://image.leevast.cn/leevastcom/2/16.png"/></a>
<a href="https://image.leevast.cn/leevastcom/2/17.png"><img src="https://image.leevast.cn/leevastcom/2/17.png"/></a>
