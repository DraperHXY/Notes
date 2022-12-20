# 一、如何阅读命令的语法
[How to read command syntax](https://www.lifewire.com/how-to-read-command-syntax-2618082)  
[Linux命令行帮助文档命令语法公式格式详解和Git命令语法格式解读](https://blog.csdn.net/wq6ylg08/article/details/88919530)
# 二、网络管理命令
## 1 ifconfig
作用：配置和设置网络配置信息  
格式：ifconfig <设备名> <选项>

## 2. netstat
作用：显示网络状态  
格式：netstat <选项>
> 无权限控制  
>
 
**注意事项**  
macOS 上的 netstat 不如 Windows/Linux 好使  
一般会使用 lsof 来补充更多的功能

[netstat命令详解](https://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316661.HTML)  
[Using netstat command on mac](https://www.lifewire.com/using-netstat-command-on-mac-4176069)

## 3. lsof
作用：列出当前系统打开的文件
> 注意事项  
> 1. 有权限控制，只能看到本用户 netstat 没有
> 2. 能看到 pid，可以找到进程

## 4. ping

## 5. ipconfig
> windows 命令

作用：查看网络配置信息的命令

## 6. traceroute
作用：显示包到主机之间的路径

## 7. route
作用：显示路由表



# 三、其他命令
## ps

ps aux | grep nacos
ps -ef | grep minio

## top
> 查看 CPU、内存消耗




