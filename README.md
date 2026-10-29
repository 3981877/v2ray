# v2ray一键脚本
```
bash <(curl -sL https://github.com/3981877/v2ray/releases/download/1.0/v2ray.sh)
```

一键脚本命令（交互式选择阿里云）

请先切换到 root 用户，然后执行：

```bash
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
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



## 七、参考信息

- 阿里云 CentOS Stream 镜像地址：`https://mirrors.aliyun.com/centos-stream/`
- CentOS Stream 9 官方文档：`https://docs.centos.org/`
- DNF 仓库配置说明：`man 5 dnf.conf`
