# Flink笔记

## 虚拟机安装

设置3个虚拟机

```
192.168.10.161
192.168.10.162
192.168.10.163
```

解决虚拟机和宿主机之间ping不通问题：

“编辑|虚拟网络编辑器”调出虚拟网络编辑器页面，选择VMnet后，在桥接到的下拉菜单中可以更换桥接到的网卡

![](..\IMG\techImages\vmware_adapter_network.png)

确保[虚拟机](https://so.csdn.net/so/search?q=虚拟机&spm=1001.2101.3001.7020)中的ip地址和桥接的宿主机网卡的ip地址在同一个网段内。

确保虚拟机和宿主机的防火墙关闭

虚拟机的防火墙状态查看，systemctl status firewalld

虚拟机的防火墙关闭 systemctl stop firewalld

关闭虚拟机的防火墙自动启动 systemctl disable firewalld

此时网络应该可以ping通了