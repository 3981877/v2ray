# v2ray-centos7使用方法

更新YUM仓库源为阿里云镜像源：
```
sudo nano /etc/yum.repos.d/CentOS-Base.repo
```
将内容替换为以下内容：
```
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
```
#### 清理并重建缓存
```
sudo yum clean all
sudo yum makecache
sudo yum update
```


# v2ray一键脚本
```
bash <(curl -sL https://github.com/3981877/v2ray/releases/download/1.0/v2ray.sh)
```


 # 防火墙相关命令
 
 #### 查看防火墙状态
```
firewall-cmd --state
```   
#### 停止防火墙
```
systemctl stop firewalld.service
```   
#### 禁止防火墙开机自启
```
systemctl disable firewalld.service
```

# 甲骨文 ( oracle ) 开启 root + 密码 登录
```
sudo -i
echo root:passwd |chpasswd root
sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;
sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config;
service sshd restart
```
用户名：root

默认密码是: passwd

登录后一定要修改密码！

密码修改命令：```passwd```
