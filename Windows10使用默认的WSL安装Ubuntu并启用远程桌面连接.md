- [Windows10使用默认的WSL安装Ubuntu19.04并启用远程桌面连接](#Windows10使用默认的WSL安装Ubuntu19.04并启用远程桌面连接)
  * [一、启用WSL功能](#一、启用WSL功能)
  * [二、安装Ubuntu](#二、安装Ubuntu)
  * [三、尝试安装轻量级桌面程序进行远程访问](#三、尝试安装轻量级桌面程序进行远程访问)
  * [四、解决Ubuntu中文乱码的问题](#四、解决Ubuntu中文乱码的问题)
  * [五、安装相关环境python&&java](#五、安装相关环境python&&java)

# Windows10使用默认的WSL安装Ubuntu19.04并启用远程桌面连接

## 一、启用WSL功能

1. 首先进入**控制面板**,输入快捷键:    <u>**windows键 ** **+** **r**    **然后输入 ** **control**</u>

2. 然后选择  **程序**--> **启用或关闭windows功能**,如下:

 ![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/1.png)

3. 见上图*勾选*"**适用于Linux的windows子系统**"、"**虚拟机平台**", 单击确定.

4. 重启电脑

## 二、安装Ubuntu

1. 进入MicroSoft Store(windows 应用商店) 搜索Ubuntu可得到:

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/2.png)

2. 选择相应版本 第一个默认是最新版本. 等待安装即可.

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/3.png)

3. 如果经常使用可以添加到任务栏.单击即可看到启动页面,依据提示输入用户名和密码,密码输入两次且看不见,要细心. 进入登录系统后

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/4.png)

4. 查看当前版本信息 终端输入:  **lsb_release  -a**   终端复制可以先复制,在终端右击即可.终端内命令复制也是单击(复制)、右击(粘贴)

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/5.png)

Ubuntu 19.04版本;代号为disco(迪斯科)

5. 查看当前用户所在目录:  **pwd**

```shell
jackmin@机械革命:~$ pwd
/home/jackmin
```

实际上Ubuntu文件隐藏很深,	在windows系统的:	C:\Users\Administrator\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs
![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/6.png)



6. 尝试切换到本机windows环境: **cd /mnt/**  出现windows相应的磁盘

```shell
jackmin@机械革命:~$ cd /mnt/

jackmin@机械革命:/mnt$ ls
c  d  e  f  g
```

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/7.png)

可以看到c盘的内容跟windows系统的一样.

## 三、尝试安装轻量级桌面程序进行远程访问

1. 官网的速度较慢,建议将Ubuntu换成国内源:

* 首先进行备份:

```shell
sudo cp /etc/apt/sources.list /etc/apt/sources_init.list
```

* 其次运行:

``` shell
sudo vim /etc/apt/sources.list
```

+ 输入密码; 部分网站提示用gedit 但是没有这个命令,需要事先安装,这里就用自带的vim即可.

按键盘上   **i**  进入编辑模式,直接复制下面代码即可 (复制网上面的 需要注意Ubuntu版本问题,本系统代号 **disco** )

```shell
deb https://mirrors.ustc.edu.cn/ubuntu/ disco main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ disco main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ disco-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ disco-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ disco-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ disco-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ disco-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ disco-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ disco-proposed main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ disco-proposed main restricted universe multiverse

```

+ 然后按  **esc**  键推出编辑转为命令模式;

+ 在按  **":"**   输入  **wq**  保存

+ 如果提示没有<u>权限编辑</u>,可以使用chmod更改权限:

```shell
chmod [-可选参数][<权限范围>+/-/=<权限设置>] 文件/目录
```

例如:

> [ u / g / o / a ] 为权限范围，其中 
> u：User，即文件或目录的拥有者 
> g：Group，即文件或目录的所属群组 
> o：Other，除了文件或目录拥有者和所属群组外，其他用户都属于这个范围 
> a：All，即全部用户
>
> 权限操作 
> +表示增加权限 
> -表示取消权限 
> =表示取消之前的权限，并给予唯一的权限
>
> 权限代号 
> r：读取权限，数字代号为 “4” 
> w：写入权限，数字代号为 “2” 
> x：执行权限，数字代号为 “1” 
> -：不具备任何权限，数字代号为 “0”
>
> 例如:
>
> ```shell
> sudo chmod u+rw /code/readme.txt
> ```
>
> 给 User 用户增加了对”/code/readme.txt”文件 “w” 和 “x” 的权限

+ 可以使用下面命令更改sources.list文件读写权限:

```shell
sudo chmod ugo+rwx /etc/apt/sources.list
```

+ 然后再次运行:

``` shell
sudo vim /etc/apt/sources.list
```

+ 按  **i**  进入编辑模式,输入上述国内源即可

然后按  **esc**  键推出编辑转为命令模式;

在按  **":"**   输入  **wq**  保存

+ 尝试更新

```shell
sudo apt-get update
sudo apt-get upgrade
```

+ 等待能看到更新的源从  https://mirrors.ustc.edu.cn/ubuntu/ disco访问而不是官网.

2. 尝试安装轻量级桌面,有很多种可供选择,这里就用 **`xrdp`** , **`xubuntu-desktop`**

   ```shell
   sudo apt-get intall xrdp
   sudo apt-get intall xubuntu-desktop
   ```

3. 安装完毕后需要启动相关服务

   ```shell
   echo xfce4-session >~/.xsession
   sudo service xrdp restart
   ```

   出现如下

   Restarting Remote Desktop Protocol server                                                  

   [20190719-15:20:51] [DEBUG] Testing if xrdp can listen on 0.0.0.0 port 3390.
   [20190719-15:20:51] [DEBUG] Closed socket 6 (AF_INET6 :: port 3390)

   ![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/8.png)

   说明可以通过windows本机远程访问本地<u>127.0.0.1:3390</u>  `3390` 是端口号

4.  （不要关闭终端）再回到windows界面,按快捷键 <u>**windows + r**  **输入mstsc**</u>

   ![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/9.png)

   输入127.0.0.1:3390,勾选始终要求凭据,点击连接

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/10.png)

成功!!!

尝试在Ubuntu桌面创建一个a.txt看能否在远程看到

```shell
cd ../
ls
cd Desktop/
touch a.txt
```

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/11.png)

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/12.png)

成功！！！

如果遇到运行,按钮提示Failed to execute  child process...

```shell
sudo apt install libexo-1-0
```

## 四、解决Ubuntu中文乱码的问题

1. 打开含有中文的网页或者一些文本,就会出现空格乱码字符,这里给出解决方法:

+ 先安装中文支持包language-pack-zh-hans

```shell
sudo apt-get install language-pack-zh-hans
```

- 然后,修改系统环境/etc/environment最后一行添加下面内容 vim  /etc/environment可能会有权限问题见前面介绍

```c
LANGUAGE="zh_CN:zh:en_US:en"
LANG="zh_CN.UTF-8"
```

* 再次修改或者创建/var/lib/locales/supported.d/local 进入到suppored.d/目录时候查看是否有local,没有直接创建一个文件local

```shell
cd /var/lib/locales/supported.d/
sudo touch local		# 创建一个文件local
```

​       添加下列

```
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_CN.GBK GBK
zh_CN GB2312
```

* 最后执行 sudo locale-gen

```shell
sudo locale-gen
```

2. 对于中文乱码是空格的情况，安装中文字体解决

```shell
sudo apt-get install fonts-droid-fallback ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
```

3. 建议1，2都执行,重新登陆，结果成功如下，不是乱码空格了。

   ![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/13.png)

   

##　五、安装相关环境 python, java

1.安装python

```
sudo apt install python      # 安装python2
sudo apt isntall python3	 # 安装python3
sudo apt install python-pip  # 安装python2的pip,使用为pip install xxx
sudo apt install python3-pip # 安装python3的pip.使用为pip3 install xxx
```

2.python换源

* 一般python的官网源比较慢,建议使用国内源，可以临时使用，也可永久使用，这里推荐永久的方法:

* 查看linux文件目录是否有/.pip/目录,没有创建,然后目录下添加pip.conf文件

```shell
mkdir ~/.pip/pip.conf
```

* 进入目录打开pip.conf文件,写入如下文件

```shell
vim ~/.pip/pip.conf
# 按 i 进入编辑模式,复制下面内容(用的是中科大的python源),需要添加信任的host
[global]
index-url = http://pypi.mirrors.ustc.edu.cn/simple/
timeout = 6000
trusted-host = pypi.mirrors.ustc.edu.cn
```
3. 安装  **Java**

   官网下载最新的 [jdk]:<https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html>

   勾选accept. linux环境选择64位，下载debain版本.deb后缀或者.tar.gz后缀。我用的是.deb

- 下载好后进入文件目录我的在Downloads/

```shell
hadoop@机械革命:/home/jackmin/Downloads$ ls
jdk-12.0.2_linux-x64_bin.deb
```

- 安装jdk输入命令  **`dpkg -i jdk-12.0.2_linux-x64_bin.deb`  **

- 安装后不知道目录怎么查看？

  先看dpkg 的列表里面有没有安装   **`dpkg -l jdk*`**

```shell
hadoop@机械革命:/home/jackmin/Documents$ dpkg -l jdk*
期望状态=未知(u)/安装(i)/删除(r)/清除(p)/保持(h)
| 状态=未安装(n)/已安装(i)/仅存配置(c)/仅解压缩(U)/配置失败(F)/不完全安装(H)/触发器等待(W)/触发器未决(T)
|/ 错误?=(无)/须重装(R) (状态，错误：大写=故障)
||/ 名称           版本         体系结构     描述
+++-==============-============-============-===============================================
ii  jdk-12.0.2     12.0.2-1     amd64     Java Platform Standard Edition Development Kit
```

​		查看所在位置  **`dpkg -L jdk-12.0.2`**

```shell
hadoop@机械革命:/home/jackmin/Documents$ dpkg -L jdk-12.0.2
/.
/usr
/usr/lib
/usr/lib/jvm
/usr/lib/jvm/jdk-12.0.2
/usr/lib/jvm/jdk-12.0.2/release
....
```

​		可以知道在/usr/lib/jvm里面.完全可以一开始就创建一个可以读写(chmod)的目录,然后安装时指定到这个目录		中

- 可以进入目录查看/usr/lib/jvm/jdk-12.0.2/

```shell
hadoop@机械革命:/usr/lib/jvm/jdk-12.0.2$ ls
bin  conf  include  jmods  legal  lib  man  release
```

- **配置linux下 <font color=red>java环境变量</font> **

打开 `/etc/profile  `添加JAVA_HOMEM 和 JAVA_PATH 和 CLASSPATH

```shell
hadoop@机械革命:/usr/lib/jvm/jdk-12.0.2/bin$ vim /etc/profile
```

(如果提示没有读写权限,用chmod,见前面内容)

`shift + g` 迅速定位到最后一行行首,新添加一行输入:

```shell
# 等号'='两边不能有空格
export JAVA_HOME=/usr/lib/jvm/jdk-12.0.2	# 根据具体路径添加
export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH	# 添加/bin  /jre/bin
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_home/jre/lib		# 注意前面.: 添加/lib  /jre/lib
```

保存退出 `:wq`

为了使环境变量生效,需要进行 `source  /etc/profile`

```shell
$ source /etc/profile
$ java -version
java version "12.0.2" 2019-07-16
Java(TM) SE Runtime Environment (build 12.0.2+10)
Java HotSpot(TM) 64-Bit Server VM (build 12.0.2+10, mixed mode, sharing)
```

因为每次开机都需要运行 source  /etc/profile,我们可以进入.bashrc然后添加 `source /etc/profile`

```shell
# 进入cd ~  
# 然后ls -la 找到.bashrc  然后vim .bashrc
# 在里面定位shift + g 最后一行插入 source /etc/profile

```

Bingo!!!
