后来经过一番搜索发现，XShell配置文件中写入了强制升级时间，这个版本是2017年12月27日发布的；2018年12月25日后就必须升级。
一、    最简单的临时解决方案：修改本地系统时间
把系统时间改到2018年12月25日之前，就可以打开了。
但是这只能解燃眉之急，治标不治本，总不能每次要打开Xshell都修改一下本地时间，打开软件后再手动修改回来吧。
二、    推荐解决方案：修改安装目录下的nslicense.dll
1. 用二进制编辑器（UltraEdit、notepad++的HEX-Editor插件）打开Xshell/Xftp安装目录下的 nslicense.dll
2. 搜索
7F 0C 81 F9 80 33 E1 01 0F 86 80
替换为：
7F 0C 81 F9 80 33 E1 01 0F 83 80
3. 保存退出即可
注：直接打开nslincense.dll可能没有编辑权限，可以copy一份到其他地方，然后进行修改，再将修改后的dll文件替换掉Xshell、Xftp安装目录下的dll
本文适用于Xsehll、Xftp 5，也适用于Xshell、Xftp 6，5和6的区别仅仅在于：
版本5的十六进制串为：7F 0C 81 F9 80 33 E1 01 0F 86 80，
版本6的十六进制串为：7F 0C 81 F9 80 33 E1 01 0F 86 81，但不影响。