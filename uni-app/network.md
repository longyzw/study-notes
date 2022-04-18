# 网络及调试

## 真机无线

前提：(port => 端口号)
1.Android设备需要开启USB调试。
2.Android设备要和PC在同一个局域网。
3.adb wifi连接成功后应该拔出USB线，不然出现adb同时连接两个设备的问题。
4.执行命令”adb tcpip port“后可能需要重新开启USB调试。
5.断开wifi连接：adb disconnect



cd 到 HBuilderX的adb目录
HBuilderX\plugins\launcher\tools\adbs

1：Android设备开启USB调试，并且通过USB线连接到电脑。
2：cmd命令执行以下命令”adb tcpip port“ 使手机监听这个端口。
3：cmd命令执行以下命令”adb connect 192.168.x.xx“(192.168.x.xx为Android设备的IP地址，WiFi网络可以查看局域网ip)。此时拔出USB线，adb通过wifi调试Android设备。
如果需要恢复到USB数据线，可以在命令行输入adb usb，也可以adb disconnect 192.168.x.xx:port