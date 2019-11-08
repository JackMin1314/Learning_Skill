[TOC]



# Linux 学习记录

## 一、相关问题

### 1. 解决E：无法获得锁/var/lib/apt/lists/lock-open（11资源暂时不可用），无法对状态列表目录加锁

```shell
sudo rm /var/lib/apt/lists/lock
```

运行sudo lsb_release -a 查看ubuntu代号时，提示执行正常但是提示No LSB modules are available。

``` shell
sudo apt-get install lsb-core
```

再次运行即可看到 LSB Version 有提示。

### 2. 时区理解GMT，UTC，CST和时间戳

> UTC（Coordinated Universal Time）是世界标准时间，世界同一时间，世界协调时间，是一个标准而不是一个时区。使用一天 24 小时时间制，并结合了地球的自转时间与原子钟的高精度度量
>
> GMT（Greenwich Mean Time）是格林威治标准时间，但从1972年开始，GMT 就不再是一个时间标准了，仅仅是一个时区的名字
>
> CST却同时可以代表如下 4 个不同的时区：（美国，澳大利亚，中国，古巴）
>
> Central Standard Time (USA) UT-6:00
>
> Central Standard Time (Australia) UT+9:30
>
> <font color=black>China Standard Time UT+8:00</font>
>
> Cuba Standard Time UT-4:00

时间戳一般指的是 unix时间戳10位（精确到秒数，13位精确到毫秒），从1970年1月1日 0：0：0（GMT）开始到现在的秒数。

命令行输入：

```shell
date   # 输出当前时间信息，也可以指定格式输出
2019年 09月 05日 星期四 10:45:59 CST
date  +%s  # 输出当前unix时间戳，单位秒（十进制）
1567651512
date  -d @1567651512  # 输出该时间戳下对应的时间，可以指定格式输出
2019年 09月 05日 星期四 10:45:12 CST
```

linux下进制转换，printf {格式} {值}

```shell
printf  %x  1567651512  # 十进制转换成十六进制
5d7076b8%  
printf  %d  0x5d7076b8  # 十六进制转换成十进制，前面加0x表示十六进制转八进制用 %o （字母o）
```



### 3. 不能连接到 ppa.launchpad.net：W: 无法下载 http://ppa.launc...等提示

有时候安装第三方软件，此时就需要先添加该软件的PPA源，再安装该软件。

```shell
# 添加：
sudo add-apt-repository  ppa:app-name/ppa-name
# 更新并安装
sudo apt-get update
sudo apt-get install xxx
# 删除：
sudo add-apt-repository -r  http://ppa.launchpad.net/fossfreedom/indicator-sysmonitor/ubuntu
```

再次运行sudo apt-get upgrade就没有之前的问题了

### 4. Linux卡死，界面只有鼠标能用

Linux 死机有很多种情况，最常见的是系统负载过高导致的。可以是运行内存耗用极大的程序，也会迅速提升系统负载。

这里提供一种方法.只通过命令行解决。

快捷键输入 `ctrl`+`alt`+`F3` (尝试按F1～F6)会出现黑色弹窗即可；然后提示你输入 **用户名和密码**，输入完毕回车就进入终端界面了，然后输入`top` 查看那些比较消耗资源(依据cpu、mem)的进程一般在最上面，找到PID，迅速按下`q`；

然后输入 sudo kill  <pid>,例如sudo kill 1434.

最后按alt + print 回到桌面，重新登录，然后update，upgrade更新系统，重启即可。

```shell
top
q
sudo kill  PID_Num1   PID_Num2   PID_Num3...
# 同时按 Alt + Print 按键，回到桌面；有时候会让你重新登录。
------------
# 成功进入后打开新的终端更新系统
sudo apt-get update
sudo apt-get upgrade
```

### 5.How to Upgrade my Ubuntu Version

``` shell
update-manager -c -d

```

## 二、相关操作

### 1. 如何更新ubuntu内核5.0->5.3

系统跟内核是不一样的，例如Ubuntu14和Ubuntu18.04，一般叫做版本号，每个版本号都有一个代号bionic，disco等等；同样他们的内核也是不一样的，例如kernel 3.6和kernel5.0，但是内核可以升级。

如何升级？内核版本多样该选择那个？

建议选择stable稳定版本的。

```shell
# 进入官网下载网速慢的用vpn帆强，选择stable下载，然后把原来的系统config放到新的解压后的目录中，具体查询百度
https://www.kernel.org/
```

这里用简单的升级内核的方法

```shell
# 进入主线的kernel内核，网址见下，选择相应版本，注意选择Build for amd64那块的
https://kernel.ubuntu.com/~kernel-ppa/mainline
```

![linux_kernel](https://github.com/JackMin1314/PicturesFiles/blob/master/linux_kernel.png)

选择四个后缀为.deb的即可，见图两个header，一个image，一个module（含有generic）

> https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.3/linux-headers-5.3.0-050300_5.3.0-050300.201909152230_all.deb
>
> https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.3/linux-headers-5.3.0-050300-generic_5.3.0-050300.201909152230_amd64.deb
>
> https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.3/linux-image-unsigned-5.3.0-050300-generic_5.3.0-050300.201909152230_amd64.deb
>
> https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.3/linux-modules-5.3.0-050300-generic_5.3.0-050300.201909152230_amd64.deb

下载完后

>  然后更改权限，复制到 /usr/src路径下，然后解压安装
>
>  `sudo  dpkg   -i  *.deb`
>
> 等待完成后，重启系统
>
>  `reboot` 

### 2. 如何创建图标到桌面

首先进入/usr/share/applications/路径下，创建xxxx.desktop文件。以创建pycharm为例

```shell
cd /usr/share/applications/
sudo touch  Pycharm.desktop  # 这个不是桌面显示的文件，是必须创建这个后缀文件并且写上代码后才可以,可理解脚本
sudo chmod 777 Pycharm.desktop # 这个是为了修改文件可以保存
vim Pycharm.desktop

```

> [Desktop Entry]
> Name=Pycharm
> Comment=Pycharm-2019.1.3--Python IDE
> Exec=/home/jackmin/MyApplication/Pycharm-2019.1.3/bin/Pycharm.sh
> Terminal=false
> Type=Application
> Icon=/home/jackmin/MyApplication/Pycharm-2019.1.3/bin/Pycharm.png
> Categories=Pycharm;

然后保存，`esc`->`:`->`wq` 然后回车。可在左下角 *显示应用程序* 中看到图标。

相关参数说明,这里以GoLand为例：

```shell
[Desktop Entry]    # 创建一个Desktop Entry实体，固定用法
Name=GoLang      # 桌面的图标名称
Comment=GoLang-2019.2.3--Go IDE       # 鼠标放在上面的提示信息
Exec=/home/jackmin/MyApplication/GoLand-2019.2.3/bin/goland.sh  # 执行程序的路径，以实际安装的具体路径为准
Terminal=false       # 打开程序后任务栏是以终端图标（黑框）显示，false为用自带的程序图标显示
Type=Application  # 类型
Icon=/home/jackmin/MyApplication/GoLand-2019.2.3/bin/goland.png  # 桌面呈现的图标的路径，以实际安装路径为准
Categories=GoLand;

```

### 3. 如何卸载添加仓库的PPA源

　PPA，英文全称为 Personal Package Archives，即个人软件包档案。是 Ubuntu Launchpad 网站提供的一项源服务，允许个人用户上传软件源代码，通过 Launchpad 进行编译并发布为二进制软件包，作为 apt / 新立得（Synaptic）源供其他用户下载和更新。
　　
　　PPA 的一般形式是： ppa:user/ppa-name

**添加 PPA 源**
　　添加 PPA 源的命令为：sudo add-apt-repository ppa:user/ppa-name
　　添加好记得要更新一下： sudo apt-get update

**删除 PPA 源**
　　删除 PPA 源的命令格式则为：sudo add-apt-repository -r ppa:user/ppa-name

例如：sudo add-apt-repository -r https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu

　　然后进入 /etc/apt/sources.list.d 目录，将相应 ppa 源的保存文件删除。也可以直接将 /etc/apt/sources.list自己添加的无效源删掉或者注释
　　最后同样更新一下：sudo apt-get update

