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
### 6. 如何批量删除指定程序的所有进程
linux 下查看进程情况用ps -ef，会显示当前所有进程，借助管道符grep可以操作和输出我们需要的信息。以nginx为例，杀死所有进程。

* 方法一：
``` shell
ps -ef|grep nginx|grep -v grep|cut -c 9-16|xargs sudo kill -9
```
下面对这个命令解释说明。
> ps -ef 查看所以的进程信息
>
> grep nginx 从前面的到的所有信息中找到有关nginx字符的信息
>
> grep -v grep 表示除去含有grep信息的(例如去除"jackmin     8403    6997  0 15:46 pts/0    00:00:00 **grep** --color=auto --exclude-dir=.bzr --exclude-dir=CVS")
>
> cut -c 9-16 表示输出信息中截取第9个到第16个字符（pid号）
>
> xargs sudo kill -9 将前面得到的结果作为参数传递给kill -9，这里kill操作需要root权限执行。
>
> xargs的默认命令是echo，这意味着通过管道传递给xargs的输入将会包含换行和空白，不过通过xargs的处理，换行和空白将被空格取代;它能够捕获一个命令的输出，然后传递给另外一个命令


* 方法二：
```shell
ps -ef |grep nginx|grep -v grep|awk '{print $2}'|xargs sudo kill -9
```
awk '{print $2}'  其中awk按行扫描文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行操作（这里是输出第二列，进程号）。

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
### 4. 下载了（WSL）ubuntu如何升级、换源和配置环境

​		这里用WSL举例，因为是最新的win10预览版本，开启wsl2 。具体如何安装参考[教程]([https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10%E4%BD%BF%E7%94%A8%E9%BB%98%E8%AE%A4%E7%9A%84WSL%E5%AE%89%E8%A3%85Ubuntu%E5%B9%B6%E5%90%AF%E7%94%A8%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E8%BF%9E%E6%8E%A5.md](https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10使用默认的WSL安装Ubuntu并启用远程桌面连接.md)).

管理员身份运行powershell输入 `wsl --set-default-version 2 `

查看当前的wsl版本信息 `wsl -l -v` 看到version是2即可。

> NAME                   STATE           VERSION
> Ubuntu                 Running         2

启动Ubuntu后，默认从商店安装的不一定时最新的版本，故需要升级，但是考虑网络访问问题，**先换源，再升级**。

* 换源需要遵循版本对应，通过查看`lsb_release -a`知道codename为 `bionic`（这个后面要用）

> No LSB modules are available.
> Distributor ID: Ubuntu
> Description:    Ubuntu 18.04.4 LTS
> Release:        18.04
> Codename:       bionic

```shell
# 先备份
sudo mv /etc/apt/sources.list /etc/apt/sources.list.bank
# 再创建并编辑（也可以直接编辑不备份）
sudo vim /etc/apt/sources.list
# 复制下面的内容，将codename换成上面查到的代号 bionic(以阿里的源为例)如下，如果是disco就换disco
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

然后更新下,并安装

```shell
sudo apt-get update && sudo apt-get upgrade
```

* 升级前先切换成root用户

```shell
sudo su
do-release-upgrade
```

这里会提示需要修改一个文件

> Checking for a new Ubuntu release
> There is no development version of an LTS available.
> To upgrade to the latest non-LTS develoment release
> set Prompt=normal in /etc/update-manager/release-upgrades.

```shell
sudo vim /etc/update-manager/release-upgrades
# 然后修改lts为normal
Prompt=normal
```

运行`do-release-upgrade` 安装确认即可；如果不换源，会导致升级很慢。我这里就直接从18.04lts升到了19.10

重新查看版本信息`lsb_release -a`时codename变成了eoan

这时候在查看`/etc/apt/sources.list` 文件已经自动被改为eoan

* 安装oh-my-zsh

```shell
# 安装zsh包
sudo apt-get install zsh
# 切换终端shell默认的bash为zsh
sudo chsh -s /bin/zsh
# 重启下reboot

# 安装zsh，需要提前安装好git
sudo apt-get install git
# 更新下
sudo apt-get update && sudo apt-get upgrade
# 安装oh-my-zsh
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
# (可选)安装语法高亮和自动补全
cd ~/.oh-my-zsh/plugins
# 语法高亮
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
# 自动补全
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
#　然后需要在~/.zshrc声明下这几个插件，找到plugins=()里面添加
vim ~/.zshrc
....
plugins=(
git
zsh-syntax-highlighting
zsh-autosuggestions
)
# 保存source ~/.zshrc退出重登即可
```

如果有换中文、乱码等情况，请参考这里[教程]([https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10%E4%BD%BF%E7%94%A8%E9%BB%98%E8%AE%A4%E7%9A%84WSL%E5%AE%89%E8%A3%85Ubuntu%E5%B9%B6%E5%90%AF%E7%94%A8%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E8%BF%9E%E6%8E%A5.md](https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10使用默认的WSL安装Ubuntu并启用远程桌面连接.md))

### 5. Python、Java、Go环境变量设置

* Java安装和环境配置参考原先[教程]([https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10%E4%BD%BF%E7%94%A8%E9%BB%98%E8%AE%A4%E7%9A%84WSL%E5%AE%89%E8%A3%85Ubuntu%E5%B9%B6%E5%90%AF%E7%94%A8%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E8%BF%9E%E6%8E%A5.md](https://gitee.com/JackMin1314/Learning_Skill/blob/master/Windows10使用默认的WSL安装Ubuntu并启用远程桌面连接.md))

也可以安装默认的openjdk `sudo apt install default-jdk` 

* Python安装和环境配置

> 下载Python-3.8.2.tar.xz文件后，解压到/usr/local/路径
>
> ``` shell
> sudo tar -C /usr/local -xvf Python-3.8.2.tar.xz
> cd /usr/local/Python-3.8.2
> # 在安装前需要先安装一些依赖
> sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget
> # 执行configure脚本，优化python二进制文件
> ./configure --enable-optimizations
> # 启动构建Python过程
> make -j 8
> # 构建完成安装python文件，不使用install是为了避免将本地有自带的python3覆盖
> sudo make altinstall
> # 添加环境变量或者建立软链接
> sudo ln -s /usr/local/Python-3.8.2/python /usr/local/bin/python
> # 输入查看版本(不建立软链接，直接Python3.8 -V也是可以的)
> $ python -V
> Python 3.8.2
> # 添加pip软链接,并升级
> sudo ln -s /usr/local/bin/pip3.8 /usr/local/bin/pip
> $ pip -V
> pip 19.2.3 from /usr/local/lib/python3.8/site-packages/pip (python 3.8)
> # 升级pip
> pip install --user --upgrade pip
> # 对python换源
> sudo mkdir -m=777 ~/.pip/
> sudo vim pip.conf
> # 输入
> [global]
> index-url = http://mirrors.aliyun.com/pypi/simple/
> timeout = 6000
> trusted-host = mirrors.aliyun.com
> 
> ```

* Go安装和环境配置

> 类似Python的做法，下载go1.14.linux-amd64.tar.gz文件后，解压到/usr/local路径(推荐)
>
> ```shell
> sudo tar -C /usr/local -zxvf go1.14.linux-amd64.tar.gz
> cd /usr/local/go
> # 这里go不需要想Python一样编译，已经是生成好的了，所以添加到环境变量即可，或者添加软链接。建议go添加环境变量，然后还需要添加工作目录
> # 在终端的配置文件中添加环境变量(等号左右无空格)
> sudo vim ~/.zshrc
> # 在前面添加
> export GOROOT=/usr/local/go/bin
> export PATH=$GOROOT:$PATH
> # 测试版本
> $ go version
> go version go1.14 linux/amd64
> # 添加工作路径(本人在$home路径创建)
> sudo mkdir -m=777 $HOME/Projects_repo/go
> # 进入目录创建src、pkg、bin目录
> # src ---- 里面每一个子目录，就是一个包。包内是Go的源码文件
> # pkg ---- 编译后生成的，包的目标文件
> # bin ---- 生成的可执行文件
> # 再次修改~/.zshrc。将之前的PATH追加$HOME/Projects_repo/go
> export PATH=$GOROOT:$HOME/Projects_repo/go:$PATH
> # 后面开发go的话在src目录下建项目文件夹,然后再在项目文件中添加.go程序
> ```

### 6. ubunt19.10安装docker和镜像加速设置

> ## 前言
>
> For WSL:
>
> 因为之前docker不支持最新版的ubuntu19.10文档一直没有更新，现在官方已经支持了。例外，由于WSL属于win10高版本，对于WSL版本为1是不能直接运行使用的（但不代表不支持），需要配合windows环境（守护进程模式）使用；本质上通信是C/S架构的。因而要借助Windows的docker根据远程ip访问。
>
> 如果现在已经安装我上面说的开启了WSL2（需要开启了Windows Insider模式，预览版用户）,本人当前电脑`win10  version 2004  OS build 19041.113`.现在可以使用 localhost 从 Windows 访问 Linux 网络应用程序.其次，最新版的Windows docker也支持了wsl2，安装了Windows版本的docker，就可以直接在wsl里面使用docker。（简单设置即可）
>
> 至于加速，如果有阿里云或者其他云厂商账号，可以在镜像加速服务里找到自己的私有镜像加速register.
>
> 如果是windows用户，直接在docker desktop的设置中docker enginer的configure添加，同上位置
>
> For Linux
>
> [官网传送门](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
>
> 如果是linux用户在`vim /etc/docker/daemon.json`里面添加；如果没有私有的镜像加速，添加官方的国内镜像，或者阿里其他的官网镜像即可
>
> ```json
> {
>  "registry-mirrors": ["https://registry.docker-cn.com","私有的镜像服务器地址"]
> }
> ```

### Vmware安装linux启动提示 Device/Credential Guard are not compatible.

>  报错问题：
>
>  VMware Workstation and Device/Credential Guard are not compatible. VMware Workstation can be run after disabling Device/Credential Guard. Please visit http://www.vmware.com/go/turnoff_CG_DG for more details.

windows键+r打开运行窗口输入`msinfo32`可以看到基于虚拟化的安全为正在运行（running）

![image-20200504091708223](C:\Users\MECHREVO\AppData\Roaming\Typora\typora-user-images\image-20200504091708223.png)

通过搜索 `Turn windows features on or off`（也可以在控制面板里面找到）如图标注配置。

![image-20200504094319954](C:\Users\MECHREVO\AppData\Roaming\Typora\typora-user-images\image-20200504094319954.png)

这里取消勾选`Hyper-V` 、`Virtual Machine Platform`、`Windows Hypervisor Platform`。保存根据提示restart PC即可。(注：如果需要同时使用docker desktop，可能上面的Hyper-V就会要求打开，跟VMware就会冲突。)

再次运行`msinfo32` 会发现基于虚拟化的安全值为`not enabled`


