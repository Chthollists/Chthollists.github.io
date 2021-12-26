---
title: Tech-Share系列CP2——Windows蓝屏诊断
reward: true
declare: true
gitalkenable: true
categories: Technology
toc: true
top: 6
tags:
  - BlueScreenView
  - ntoskrnl.exe
abbrlink: 85eaa08e
date: 2021-12-12 22:18:36
---
---

* Windows蓝屏诊断+解决小秘诀
  * 系统蓝屏日志输出配置
  * 蓝屏诊断方式与工具
  * 蓝屏问题修复

<!-- more -->

# Tech-Share系列CP2——Windows蓝屏诊断

## 1. Windows蓝屏诊断

### 1.1 系统日志配置

* 蓝屏原因
  * 软件兼容性、冲突
  * 硬件问题
* 系统日志`DMP`文件输出设置
  * 第一步：右键"此电脑"，选择"属性"，找到系统下的高级系统设置项
  
    ![高级系统设置](https://gitee.com/chthollists/PicRepo/raw/master/Technology/BlueScreen/%E9%AB%98%E7%BA%A7%E7%B3%BB%E7%BB%9F%E8%AE%BE%E7%BD%AE.bmp)
  
  * 第二步：进入高级系统设置项，选择高级，点击启动和故障恢复项的设置
  
    ![启动与故障设置](https://gitee.com/chthollists/PicRepo/raw/master/Technology/BlueScreen/%E5%90%AF%E5%8A%A8%E5%92%8C%E6%95%85%E9%9A%9C%E6%81%A2%E5%A4%8D%E8%AE%BE%E7%BD%AE.bmp)
  
  * 第三步：在菜单中勾选将事件写入系统日志，并将选项写入调试信息设置为：核心内存转储、或小内存转储(256KB)，确认设置
  
    ![故障日志设置](https://gitee.com/chthollists/PicRepo/raw/master/Technology/BlueScreen/%E6%95%85%E9%9A%9C%E6%97%A5%E5%BF%97%E8%AE%BE%E7%BD%AE.bmp)
  
  * 设置完成后，当系统蓝屏后会自动保存事件日志文件，文件目录为`%SystemRoot%\MiniDump`

### 1.2 蓝屏诊断工具

* 蓝屏诊断：分析`DMP`日志文件

  * `WinDbg`软件：专业人士
  * `BlueScreenView`软件：普通用户

* `BlueScreenView`蓝屏分析器的使用

  * 第一步：点击第一个图标"高级选项"，勾选从以下`MiniDump`文件夹载入，默认路径为`C:\Windows\MiniDump`

    ![BlueScreenView设置](https://gitee.com/chthollists/PicRepo/raw/master/Technology/BlueScreen/BlueScreenView%E8%AE%BE%E7%BD%AE.bmp)

  * 第二步：选择近期蓝屏导致的日志文件，根据时间来对应，可以看到报错的原因，红色的文件就是蓝屏的原因

    ![蓝屏分析](https://gitee.com/chthollists/PicRepo/raw/master/Technology/BlueScreen/%E8%93%9D%E5%B1%8F%E5%88%86%E6%9E%90.bmp)

  * 第三步：定位了出错的文件，即可查询该文件出错是由于软件兼容性冲突、缺少驱动等错误原因，针对性的进行修复

---

## 2. 蓝屏修复方案

### 2.1 常见错误

* 硬件、驱动问题
  * `nvlddmkm.sys`、`dxgmms2.sys`、`igdkmd64.sys`：是英伟达(NVIDIA)显卡驱动，通过官方渠道重新安装独立显卡和核心显卡的硬件驱动程序
  * `dxgkrnl.sys`：是DirectX的驱动文件，需要通过DirectX修复文件进行修复
  * `ntkrnlmp.exe`：Windows系统内核，通过官方渠道重新安装电脑所有主要硬件 (包括 BIOS、独立和核心显卡、有线和无线网卡、声卡等) 的驱动程序
  * `NETIO.SYS`、`tcpip.sys`、`vwififlt.sys`、`wdiwifi.sys`、`rt640x64.sys`：电脑网卡硬件相关驱动程序，通过官方渠道重新安装有线网卡和无线网卡的硬件驱动程序
  * `iaStorAC.sys`：Intel 快速存储技术驱动程序，通过官方渠道重新安装"Intel 快速存储技术"驱动程序
* 软件兼容性冲突
  * `topsecpf.sys`：天融信厂商旗下应用程序，卸载电脑中的天融信厂商旗下软件
  * `360Hvm64.sys`：360 安全卫士应用程序，卸载电脑中的360厂商旗下软件
  * `TesSafe.sys`：腾讯电脑管家、腾讯游戏等应用程序，卸载电脑中的腾讯厂商旗下软件
  * `aida64.exe`：AIDA 64硬件检测工具，卸载电脑中的 AIDA 64 工具。
  * `xlwfp.sys`：迅雷厂商旗下应用程序，卸载电脑中的迅雷厂商旗下软件。
  * `chrome.exe`：谷歌旗下 Chrome 浏览器，卸载电脑中的 Google Chrome 浏览器
  * `HaozipSvc.exe`、`WdFilter.sys`、`Pic_2345Svc.exe`、`2345ExProtec`、`2345Iron.sys`、`2345Misc.sys`、`2345Prot.sys`、`2345Base.sys`等：与2345系列流氓软件相冲突，需要卸载2345老流氓

### 2.2 本机错误示例：ntoskrnl.exe

* 蓝屏原因：`ntoskrnl.exe`

  * Windows 操作系统的一个重要内核程序文件，里面存储了大量的二进制内核代码，用于调度系统
  * 在正常情况下，在任务管理器是以"System"的名字出现的
  * `ntoskrnl.exe`也可能是`w32.bolzano`病毒，如果发现异常的`ntoskrnl.exe`文件，请立即使用杀毒软件进行查杀

* 修复方案

  * 第一步：磁盘分析与修复：用管理员方式启动`cmd.exe`，执行`chkdsk C:/F`命令，重启后生效
* 第二步：修复DirectX驱动：使用**DirectX修复工具**进行修复
  * 第三步：修复C++库：使用**DirectX修复工具增强版**进行修复，可以通过普通版的菜单--工具--选项--扩展中选**开始扩展**，来升级到增强版

* 写在最后

  > 本机通过这三步修复，其中磁盘修复无法看到结果，DirectX驱动没有问题，C++库2015~2019版本存在问题，修复后，暂时不会再发生蓝屏问题。如果有人知道如何查看`chkdsk C:/F`执行后的结果，请分享在评论区。

### 参考资料

1. [Windows系统蓝屏原因排查及解决方法](https://new.qq.com/omn/20210205/20210205A09FML00.html)
2. [ntoskrnl.exe导致Win10蓝屏的解决方案 - 知乎 ](https://zhuanlan.zhihu.com/p/37619207)
3. [解决由于ntoskrnl.exe导致的蓝屏](https://blog.csdn.net/Dongguabai/article/details/83215762)

