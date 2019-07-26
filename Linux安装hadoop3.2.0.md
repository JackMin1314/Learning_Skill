
Table of Contents
=================

   * [Linux上面安装hadoop3.2.0教程](#linux上面安装hadoop320教程)
      * [一、安装java并配置环境变量](#一安装java并配置环境变量)
         * [1.安装  <strong>Java</strong>](#1安装--java)
         * [2. 配置linux下 <strong>java环境变量</strong>](#2-配置linux下-java环境变量-)
      * [二、安装配置<strong>hadoop</strong>](#二安装配置hadoop)
         * [1. 安装 <strong>hadoop</strong>](#1-安装-hadoop)
         * [2. hadoop <strong>环境变量和配置</strong>](#2-hadoop-环境变量和配置)
      * [三、SSH免密登录](#三ssh免密登录)
         * [1.SSH <strong>原理简述</strong>](#1ssh-原理简述)
         * [2. SSH <strong>免密登录</strong>](#2-ssh-免密登录)
      * [四、启动<strong>hadoop</strong>](#四启动hadoop)
         * [1. namenode<strong>格式化</strong>](#1-namenode格式化)
         * [2. 启动NameNode,DataNode daemon进程](#2-启动namenodedatanode-daemon进程)
         * [3. 查看是否成功启动hadoop](#3-查看是否成功启动hadoop)

# Linux上面安装hadoop3.2.0教程

## 一、安装java并配置环境变量

linux 终端显示光标可用  `echo -e "\033[?25h"` ,隐藏光标可用  `echo -e "\033[?25l"` 

### 1.安装  **Java**

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

### 2. 配置linux下 java环境变量 

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

Bingo!!!

## 二、安装配置**hadoop**

### 1. 安装 **hadoop**

[hadoop]: https://hadoop.apache.org/releases.html

- 选择binary,然后复制新页面的推荐地址  http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz
- 复制后命令行安装: wget  http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz

结束后可在当前路径看到生成了一个文件夹

```shell
hadoop@机械革命:~$ ls
hadoop-3.2.0  hadoop-3.2.0.tar.gz
```

### 2. hadoop **环境变量和配置**

```shell
$ vim /etc/profile		# 添加hadoop_home,以及/bin;/sbin路径
export HADOOP_HOME=/home/hadoop/hadoop-3.2.0
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$JAVA_HOME/jre/bin:$PATH
$ source /etc/profile	# 使环境变量生效
```

1. 查看是否安装完成

```shell
hadoop@机械革命:~$ hadoop version
Hadoop 3.2.0
Source code repository https://github.com/apache/hadoop.git -r e97acb3bd8f3befd27418996fa5d4b50bf2e17bf
Compiled by sunilg on 2019-01-08T06:08Z
Compiled with protoc 2.5.0
```

ok

2. 设置修改hadoop配置(伪分布模式)

   在原来/home/hadoop/hadoop-3.2.0/文件下 新建一个tmp文件夹,在tmp下新建两个文件夹命名为name,data注意文件夹的权限

- 首先修改 /etc/hadoop/core-site.xml

```html
<configuration>
<property>
         <name>hadoop.tmp.dir</name>
         <value>file:/home/hadoop/hadoop-3.2.0/tmp</value>
         <description>Abase for other temporary directories.</description>
</property>
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
</property>
</configuration>         
```

- 修改 .../hadoop-3.2.0/etc/hadoop/hdfs-site.xml 

```html
<configuration>
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:/home/hadoop/hadoop-3.2.0/tmp/dfs/name</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>file:/home/hadoop/hadoop-3.2.0/tmp/dfs/data</value>
</property>
</configuration>
```

- 然后修改`hadoop-env.sh`将里面的java_home改为之前的java_home地址

```shell
JAVA_HOME=/usr/lib/jvm/jdk-12.0.2   
```

- 最后修改mapred-site.xml

```html
<configuration>
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>
</configuration>
```

- 这时候先不启动,还没有配置ssh

## 三、SSH免密登录

### 1.SSH **原理简述**

- 简单来说,SSH是一种网络协议,用于网络主机之间的加密协议.

- 使用ssh登录的简单流程:

  > 1. 用户使用`ssh user@host`命令对远程主机发起登陆；
  > 2. 远程主机将自己的公钥返回给请求主机；
  > 3. 请求主机使用公钥对用户输入的密码(远程主机的密码)进行加密；
  > 4. 请求主机将加密后的密码发送给远程主机；
  > 5. 远程主机使用私钥对密码进行解密；
  > 6. 最后，远程主机判断解密后的密码是否与用户密码一致，一致就同意登陆，否则反之。

------

PS:

> 1. SSH不像https协议那样，SSH协议的公钥是没有证书中心（CA）公证(可信第三方)的,容易遭受**中间人攻击**.
> 2. 对于给出的远程主机公钥指纹fingerprint是否可信,由用户自己决定,
>
> 如果确信,则公钥被接受保存在 `~ /.ssh/known_hosts` 中,下次登录该主机的时候就直接读取known_hosts,忽略警告,当**远程主机公钥修改**了时,连接时会提醒需要删除本地的known_hosts(见后面4.8)

- 使用ssh免密登录的原理是:

  根据前面的原理可以知道想登录远程需要生成 公私密钥对:用 `ssh-keygen -t rsa` 生成密钥对,再用`ssh-copy-id -i ~/.ssh/id_rsa.pub user@host` 将公钥发给远程主机的`~/.ssh/authorized_keys`里面.而登录本机则直接用

  ```shell
  $ ssh-keygen -t rsa              # 会有提示，都按回车就可以
  $ cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
  ```

- 免密登录简单流程:

  > 1. 用户使用`ssh user@host`命令对远程主机发起登陆；
  > 2. 远程主机对用户返回一个随机串；
  > 3. 用户所在主机使用私钥对这个随机串进行加密，并将加密的随机串返回至远程主机；
  > 4. 远程主机使用分发过来的公钥对加密随机串进行解密；
  > 5. 如果解密成功，就证明用户的登陆信息是正确的，则允许登陆；否则反之。

### 2. SSH **免密登录**

如果你想登录其他主机,就需要将本机生成的公钥`id_rsa.pub`(私钥为id_rsa)复制给其他主机的authorized_keys.

默认hadoop进行本机免密登录也是需要提前将id_rsa_pub复制给本机authorized_keys

1. 尝试登录localhost

```shell
hadoop@机械革命:/etc/ssh$ ssh localhost
Command 'ssh' not found, but can be installed with:
sudo apt install openssh-client
```

表明本机没有安装ssh,如果提示 ssh: connect to host localhost port 22: Connection refused,可能是没有开启服务等问题. 切换root用户,如下操作:

2. 卸载SSH

```shell
 sudo apt-get autoremove openssh-server
 sudo apt-get autoremove openssh-client
 sudo apt-get purge openssh-server # 彻底卸载ssh服务
```

3. 重新安装SSH

```shell
sudo apt-get install openssh-server 
sudo apt-get install openssh-client
```

4. ssh的启动

   查看 **`服务`**是否正确启动 `ps -ef |grep ssh`

```shell
root@机械革命:/etc/ssh# ps -ef |grep ssh
jackmin   9048  8957  0 19:20 ?        00:00:00 /usr/bin/ssh-agent /usr/bin/im-launch /usr/bin/zsh /home/jackmin/.xsession
root     16213 15077  0 23:14 tty3     00:00:00 grep --color=auto ssh

```

没有出现上面的ssh-agent 和 sshd等内容时,需要完全卸载重新执行.

输入下面命令,开启服务,之后再重新查看服务

```shell
sudo service ssh start
 * Starting OpenBSD Secure Shell server sshd                       [ok]

```

```shell
root@机械革命:/etc/ssh# ps -ef |grep ssh
jackmin   9048  8957  0 19:20 ?        00:00:00 /usr/bin/ssh-agent /usr/bin/im-launch /usr/bin/zsh /home/jackmin/.xsession
root     16293     1  0 23:31 ?        00:00:00 /usr/sbin/sshd
root     16305 15077  0 23:35 tty3     00:00:00 grep --color=auto ssh

```

5. 切换到ssh目录  cd  /etc/ssh/

```
# cd /etc/ssh/
root@机械革命:/etc/ssh#

```

6. 复制导入公钥,ssh连接需要公钥认证

   ```shell
   root@机械革命:/home# ssh-keygen -t rsa -P ""
   Generating public/private rsa key pair.
   Enter file in which to save the key (/root/.ssh/id_rsa): /root/.ssh/id_rsa #需要输入
   Your identification has been saved in /root/.ssh/id_rsa.
   Your public key has been saved in /root/.ssh/id_rsa.pub.
   The key fingerprint is:
   SHA256:iOego....
   $ cat  /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
   
   ```

7. **切换到hadoop用户**(我安装在新建的用户hadoop上面,如果不切换,可直接在接着上面步骤)执行:

   进入/etc/ssh/ 修改权限并编辑sshd_config 文件,添加两行:`PubkeyAuthentication yes` 以及 `PermitRootLogin yes`

```shell
hadoop@机械革命:/home/jackmin$ cd  /etc/ssh/ 
# 更改/etc/ssh/sshd_config权限
hadoop@机械革命:/etc/ssh$ sudo chmod ugo+rwx sshd_config
[sudo] hadoop 的密码：
# 编辑sshd_config添加:PubkeyAuthentication yes以及PermitRootLogin yes
hadoop@机械革命:/etc/ssh$ vim sshd_config
# 重新启动ssh服务
hadoop@机械革命:/etc/ssh$ sudo /etc/init.d/ssh restart
 * Restarting OpenBSD Secure Shell server sshd

```

8. 尝试ssh localhost

   woo!!!出现了这个

   ```shell
   hadoop@机械革命:/etc/ssh$ ssh localhost
   @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
   @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
   @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
   IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
   Someone could be eavesdropping on you right now (man-in-the-middle attack)!
   It is also possible that a host key has just been changed.
   The fingerprint for the ECDSA key sent by the remote host is
   SHA256:L/i20njmxWwRGPeuHi/6OeoR6yxgyG00Az8ga9/MzNI.
   Please contact your system administrator.
   Add correct host key in /home/hadoop/.ssh/known_hosts to get rid of this message.
   Offending ECDSA key in /home/hadoop/.ssh/known_hosts:1
     remove with:
     ssh-keygen -f "/home/hadoop/.ssh/known_hosts" -R "localhost"
   ECDSA host key for localhost has changed and you have requested strict checking.
   Host key verification failed.
   
   ```

   应该是我之前折腾导入秘钥导致known_hosts被更改了,根据提示先删除, 执行`ssh-keygen -f “/hadoop/hadoop/.ssh/known_hosts" -R “localhost"`

   ```shell
   hadoop@机械革命:/etc/ssh$ sudo ssh-keygen -f "/home/hadoop/.ssh/known_hosts" -R "localhost"
   # Host localhost found: line 1
   /home/hadoop/.ssh/known_hosts updated.
   Original contents retained as /home/hadoop/.ssh/known_hosts.old
   
   ```

   9.再次运行ssh localhost 提示需要输入密码hadoop@localhost's password:  按`ctrl+c` 然后`exit`退出刚才的ssh服务,进入/.ssh   `cd ~/.ssh` 然后 ssh-keygen -t rsa 然后按几下回车.

   ```shell
   $ ssh-keygen -t rsa              # 会有提示，都按回车就可以
   $ cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
   
   ```

运行ssh localhost  输入yes

```
	10. 这里还要修改相应的ssh文件权限,有的没有读写权利Stopping secondary namenodes [机械革命]

```

机械革命: Failed to add the host to the list of known hosts (/home/hadoop/.ssh/known_hosts)

修改/home/hadoop/.ssh权限和 /home/hadoop/.ssh/known_hosts

```shell
hadoop@机械革命:~/hadoop-3.2.0/sbin$ sudo chmod 644 ~/.ssh/known_hosts
hadoop@机械革命:~/hadoop-3.2.0/sbin$ sudo chmod 755 ~/.ssh

```

执行ssh localhost成功

## 四、启动**hadoop**

### 1. namenode**格式化**

```shell
$ hdfs namenode -format

```

因为我们配置  `hadoop.tmp.dir` 参数,并不是默认使用的 ` /tmp/hadoo-hadoop` 执行一次格式化即可以后不用了,如果没有配置,则每次系统重启后都需要 -format

### 2. 启动NameNode,DataNode daemon进程

```shell
$ start-dfs.sh

```

一般不建议直接 `start-all.sh` 应该先启动hdfs再依次启动其他的构件.

### 3. 查看是否成功启动hadoop

```shell
$ jps
7061 Jps
3174 NameNode
3593 SecondaryNameNode

```

再从浏览器输入:http://localhost:9870   回车即可看到一片绿~
