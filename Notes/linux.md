[TOC]
### 用户
#### 查看当前所有用户
cat /etc/passwd
#### 添加用户
```bash
sudo adduser hypo
```
#### 更改密码
```bash
sudo passwd hypo
```
#### 更改用户权限
```bash
sudo vim /etc/sudoers
hypo ALL=(ALL:ALL)  ALL
```
#### 删除用户
```bash
sudo userdel -r hypo
```

### 文件管理相关
```bash
ls # 列出当前目录文件
ls -l # 详细信息，权限，修改时间等
du --max-depth=1 -h ./ #查看当前目录占用空间
```

#### 删除文件
```bash
rm -rf yourdir #-r 就是向下递归，不管有多少级目录，一并删除　-f 就是直接强行删除，不作任何提示的意思

find -name "*.pth" -delete　#批量删除后缀为pth的所有文件
```
#### cp  mv(改文件名也用这个)
```bash
# cp复制  　mv剪切
cp [options] <source file or directory> <target file or directory>
# or
cp [options] source1 source2 source3 …. directory
#example
cp -rvf dir1 dir2 #复制文件夹dir1到dir2，显示进度
cp -rf dir1 dir #复制文件夹dir1到dir2，不显示进度
#-a 保留链接和文件属性，递归拷贝目录，相当于下面的d、p、r三个选项组合。
#-d 拷贝时保留链接。
#-f 删除已经存在目标文件而不提示。
#-i 覆盖目标文件前将给出确认提示，属交互式拷贝。
#-p 复制源文件内容后，还将把其修改时间和访问权限也复制到新文件中。
#-r 若源文件是一目录文件，此时cp将递归复制该目录下所有的子目录和文件。当然，目标文件必须为一个目录名。
#-l 不作拷贝，只是链接文件。
#-s 复制成符号连结文件 (symbolic link)，亦即『快捷方式』档案；
#-u 若 destination 比 source 旧才更新 destination。
```

#### 文件夹权限
```bash
#开放文件夹及子文件夹所有权限
chmod -R 777 file #4是读权限，2是写权限，1是可执行权限，777就是所有权限都开，766或776才是开放其他用户的读写权限，777是开放所有权限
#错误使用例子
# chmod -R 777 / #开放整个系统所有权限，完蛋
# chown -R mysql / #锁死所有权限，完蛋
```

#### 压缩解压
```bash
#7z
apt-get install p7zip-full #安装
7z x file.7z #解压
7za a -t7z -mx=9 -m0=lzma2 -mmt=48 -r Mytest.7z ./test #压缩
# -mx:压缩等级，9是极限压缩，-m0：压缩算法，-mmt：使用的线程数

#.tar   .tar.xz
tar xvf filename.tar #.tar 解包 
tar cvf filename.tar dirname #.tar 打包 
tar cvzf - filedir | split -d -b 50m - filename #.tar 分卷打包 
cat x* > myzip.tar.gz #.tar 分卷解包
tar xzvf myzip.tar.gz

#.gz
gunzip filename.gz #.gz 解压1 
gzip -d filename.gz #.gz 解压2 
gzip filename #.gz 压缩 

#.tar.gz 
tar zxvf filename.tar.gz #.tar.gz 和 .tgz 解压 
tar zcvf filename.tar.gz dirname #.tar.gz 和 .tgz 压缩 

#zip
unzip filename.zip  -d filedir #.zip 解压 
zip filename.zip dirname #.zip 压缩 

#rar
rar x filename.rar #.rar 解压 
rar a filename.rar dirname #.rar 压缩 

#.tar.gz 和 .tgz   多线程压缩解压
sudo apt install pigz
#压缩
tar cvf - test.txt | pigz > test.tar.gz
#解压
tar -I pigz -xvf /path/to/archive.tar.gz -C /where/to/unpack/it/
```

### 硬件及驱动
#### 安装显卡驱动
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
ubuntu-drivers devices#选择推荐的驱动版本
sudo apt install nvidia-430
```
#### 分区/挂载U盘/硬盘
##### 分区
```bash
fdisk -l 
#先查看下是否有磁盘没有分区如果没有
#device       Start       End   Sectors   Size Type
#则该磁盘没有分区
fdisk /dev/sdb
#输入m，可以查看有哪些操作
#输入p 查看当前硬盘分区，目前没有分区
#输入n新建一个分区，输入p 建立分区，输入分区编号 1
#设置扇区起始和结束,默认就是最大化的分区
#输入p然后打印分区数，红色框就是已经建立好的分区
#最后保存分区 输入w
mkfs.ext4 /dev/sdb1
```
##### 挂载
```bash
root@lthpc:/home/hypo# fdisk -l
...
Device     Start        End    Sectors  Size Type
/dev/sdc1   2048 7813967871 7813965824  3.7T Microsoft basic data
root@lthpc:/home/hypo# cd /media  
root@lthpc:/media# mkdir usb
root@lthpc:/media# mount -t ntfs-3g /dev/sdc1 /media/usb
# 格式可以是:ext4 ext2 xfs
# 用df -h 查看是否挂载成功
#卸载挂载点
$ umount /dev/hda2
$ umount /usr
#参数可以是设备文件或安装点
```
##### 开机自动挂载
```bash
sudo vim /etc/fstab
# 在最后面加上
/dev/sdb1 /media/hypo ext4 defaults 0 0
# 设备文件 挂载点 文件系统类型 挂载参数 设备标记 检测顺序
```


### linux应用相关
#### vim
*  模式
1.vim 一打开，就会进入所谓的普通模式(Normal)。在这个模式下，大家输入的所有内容都会被 vim 解析成相应的指令并执行。<br>
2.如果要输入内容，必须键入字母 i 来命令 vim 切换到所谓的插入模式(Insert)。在插入模式下，大家就可以像在其他普通编辑器下那样输入文字了。输入完毕，需要通过按Esc返回普通模式。<br>
* 撤销重做
撤销上次操作用 u，反撤销用 ctrl+r
* 保存退出
保存退出都要在命令模式下完成。保存用 :w path/to.txt，退出用 :q!。如果是编辑已经存在的文件可以直接用 :wq 退出。
#### goldendict
```bash
# 安装
sudo apt install goldendict
sudo apt install goldendict gawk
# 添加谷歌翻译
git clone https://github.com/soimort/translate-shell
cd translate-shell/
make
sudo make install
```
打开GoldenDict，【编辑】-【词典】-【词典来源】-【程序】，点击【添加】，勾上【已启用】，填写【类型】Html 和【名称】Google，在【命令行】中输入
```
trans -e google -s auto -t zh-CN -show-original y -show-original-phonetics n -show-translation y -no-ansi -show-translation-phonetics n -show-prompt-message n -show-languages y -show-original-dictionary n -show-dictionary n -show-alternatives n ''%GDWORD%''
```

### 常用系统命令

#### deb
```bash
sudo apt-get install -f #解决依赖关系
dpkg -i <package.deb> #安装一个 Debian 软件包，如你手动下载的文件。
dpkg -c <package.deb> #列出 <package.deb> 的内容。
dpkg -I <package.deb> #从 <package.deb> 中提取包裹信息。
dpkg -r <package> #移除一个已安装的包裹。
dpkg -P <package> #完全清除一个已安装的包裹。和 remove 不同的是，remove 只是删掉数据和可执行文件，purge 另外还删除所有的配制文件。
dpkg -L <package> #列出 <package> 安装的所有文件清单。同时请看 dpkg -c 来检查一个 .deb 文件的内容。
dpkg -s <package> #显示已安装包裹的信息。同时请看 apt-cache 显示 Debian 存档中的包裹信息，以及 dpkg -I 来显示从一个 .deb 文件中提取的包裹信息。
dpkg-reconfigure <package> #重新配制一个已经安装的包裹，如果它使用的是 debconf (debconf 为包裹安装提供了一个统一的配制界面)。
```

#### date
```bash
# 查看当前时间
date
# 修改时间
sudo date -s "2020-12-29 19:43:00"
# 写入bios
sudo hwclock -w
```

#### shell 中运行基本应用
```bash
nautilus #文件管理器
firefox #火狐浏览器
gedit #文本编辑器
```
#### 批量杀死应用
```bash
ps aux|grep "python" | grep -v grep|cut -c 9-15|xargs kill -15
# 管道符“|”用来隔开两个命令，管道符左边命令的输出会作为管道符右边命令的输入。下面说说用管道符联接起来的 
#几个命令： 
#“ps aux”是linux 里查看所有进程的命令。这时检索出的进程将作为下一条命令“grep python”的输入。 
#“grep python”的输出结果是，所有含有关键字“python”的进程，这是python程序
#“grep -v grep”是在列出的进程中去除含有关键字“grep”的进程。 
#“cut -c 9-15”是截取输入行的第9个字符到第15个字符，而这正好是进程号PID。 
#“xargs kill -15”中的xargs命令是用来把前面命令的输出结果（PID）作为“kill -15”命令的参数，并执行该令。 
#“kill -15”会正常退出指定进程，-9强行杀掉
```
#### 进程暂停与继续
```bash
# 暂停PID为1234的进程
kill -STOP 1234
# 继续PID为1234的进程
kill -CONT 1234 
```

#### 调整CPU性能模式
```bash
sudo apt-get install cpufrequtils
cpufreq-info
sudo cpufreq-set -g performance
##修改默认
sudo apt-get install sysfsutils
sudo gedit  /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
```
#### 添加或禁用开机启动项
```bash
#禁用开机启动项
$ systemctl list-unit-files --type=service|grep enabled #查看开机启动的服务
$ sudo systemctl disable apache2.service #禁用掉该服务
#添加开机启动项
$ systemctl list-unit-files --type=service|grep team
teamviewerd.service                        enabled    
$ sudo systemctl enable teamviewerd.service
```



### 远程相关
#### ssh
* install
```bash
sudo apt-get install openssh-client 
sudo apt-get install openssh-server
```
* start
```bash
ps -e | grep ssh
sudo /etc/init.d/ssh start
```
* ip
```bash
ifconfig
```
* login
```bash
ssh hypo@192.168.0.1
ssh -X hypo@192.168.0.1  #GUI
```
* change port
```bash
ssh -p 1234 hypo@192.168.0.1
```
* exit
```bash
'Ctrl+D'
#orchmod -R 777 /home/mypackage
exit
```
* ssh-keygen
```bash
ssh-keygen -t rsa
ssh-copy-id hypo@192.168.0.1
```
* run code
```bash
nohup python3 a.py &
```
* 远程启动火狐(需要在图形模式下)
```bash
firefox &
```
#### ssh-windows-server
“应用”>“应用和功能”>“管理可选功能”>添加openssh
```bash
Start-Service sshd
# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'
# Confirm the Firewall rule is configured. It should be created automatically by setup. 
Get-NetFirewallRule -Name *ssh*
# There should be a firewall rule named "OpenSSH-Server-In-TCP", which should be enabled
# If the firewall does not exist, create one
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```
默认用户名为C:\Users\username的username
密码则为该用户登录密码

#### ssh-windows 免密登录 linux
```bash
# 先在powershell中执行
function ssh-copy-id([string]$userAtMachine){   
    $publicKey = "$ENV:USERPROFILE" + "/.ssh/id_rsa.pub"
    if (!(Test-Path "$publicKey")){
        Write-Error "ERROR: failed to open ID file '$publicKey': No such file"            
    }
    else {
        & cat "$publicKey" | ssh $userAtMachine "umask 077; test -d .ssh || mkdir .ssh ; cat >> .ssh/authorized_keys || exit 1"      
    }
}
#
ssh-keygen -t rsa
ssh-copy-id ldz@192.168.0.1
```


#### rdesktop(Windows远程桌面)
```bash
rdesktop -u Administrator -p password ip -g 1280x720
-r sound:local:alsa #远程声音
```
#### transmit files
```bash
#压缩+解压流传输 把本地的文件复制到远程主机上
tar -c './dir' |pigz |ssh hypo@172.31.73.116 "gzip -d|tar -xC /home/hypo/MyProject"
tar -c './DeepMosaics' |pigz |ssh hypo@172.30.194.156 "gzip -d|tar -xC /media/hypo/Project/MyProject/DeepMosaics"

#把远程的文件复制到本地
scp root@www.test.com:/val/test/test.tar.gz /val/test/test.tar.gz
#把本地的文件复制到远程主机上
scp /val/test.tar.gz root@www.test.com:/val/test.tar.gz
#把远程的目录复制到本地
scp -r root@www.test.com:/val/test/ /val
```
#### smb共享设置
* 安装smb
```bash
sudo apt-get install samba
sudo apt-get install samba-client
```
* 建立用于共享的用户及文件夹(/home/share)并设置权限
```bash
chmod 777 /home/share
```
* 修改配置文件
```bash
vim /etc/samba/smb.conf

#在文件末尾加入
[share]
    comment = share
    path = /media/harddisk/share
    public = yes
    writable = yes
    directory mask = 0775
    create mask = 0775
    valid users = share,hypo,root
    write list = share,hypo,root
    browseable = yes
    available = yes
```
* 设置smb登录账户及密码
```bash
sudo smbpasswd -a share
```
* 启动
```bash
sudo /etc/init.d/smbd start
```
* 查看smb连接
```bash
smbclient -L  //localhost
```
* win+r查看共享文(file://ip/)
* windows无法访问,在cmd中
```bash
net use * /del /y
```

### 常见系统故障
#### 百度盘 无法登录
```bash
rm -rf  ~/baidunetdisk #这个蛇皮的bug不知道啥时候才能修复
```
#### nvidia-smi command not found显卡驱动故障
```bash
# 方法一:重装驱动
sudo apt-get purge nvidia*
sudo apt-get autoremove 

sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
ubuntu-drivers devices
sudo apt install nvidia-430

# 方法二
# NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
sudo apt install dkms
sudo dkms install -m nvidia -v 418.87.00
# 其中，418.87.00 是之前安装 nvidia 驱动的版本号，可通过下面方法查到：
ls /usr/src | grep nvidia
```
#### ERROR：gzip: stdout: No space left on device(boot空间不足)
内核安装过多导致的空间不足
```bash
#查看boot占用情况
df 
#查看当前内核版本
uname -r 
#查看已安装内核版本
cd /boot
ls
# 删除多余的内核
sudo apt autoremove linux-image-4.4.0-57-generic
#查看boot占用情况
df 
```
#### ssh连接使用方向键出现乱码
原因是新建用户使用了不同的shell
```bash
vim /etc/passwd
root:x:0:0:root:/root:/bin/bash
hypo:x:1001:1001::/home/hypo:/bin/sh
```
只需要把sh改成bash就好

#### ssh秘钥失效
A机器通过ssh-copy-id root@IP(B)添加了链接到B机器的ssh秘钥。但是某天，B机器的密码修改或者机器重装，此时，在A机器上再次ssh IP(B)会报类似如下错误
```bash
ssh-keygen -f "/home/hypo/.ssh/known_hosts" -R "[IP]:poet"
```
#### OSError: [Errno 24] Too many open files
超出了进程同一时间最多可开启的文件数(一个应用调用的最大进程数)，linux中默认是1024<br>
解决办法： 在运行程序前输入```ulimit -n 2048```<br>
或者

```bash
sudo vim /etc/security/limits.conf
#最后添加两行代码
* soft nofile 4096
* hard nofile 4096
```
