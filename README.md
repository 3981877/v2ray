# v2ray一键脚本
```
bash <(curl -sL https://github.com/3981877/v2ray/releases/download/1.0/v2ray.sh)
```

重启后，之前卡死的进程已经清掉了，现在是一个干净的起点。既然国内镜像站的 Oracle Linux 9 路径反复不通，建议**彻底放弃国内镜像，直接用 Oracle 官方源**，先让 `dnf` 恢复正常工作。

### 第一步：清理所有残留的源配置

先把之前折腾过的、可能还有问题的 repo 文件全部移走，避免它们继续干扰：

```bash
sudo mkdir -p /etc/yum.repos.d/backup-all
sudo mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup-all/ 2>/dev/null
```

### 第二步：写入 Oracle 官方源

Oracle 官方源的路径结构是经过验证的，不存在“镜像站路径不存在”的问题。创建一个干净的官方源配置：

```bash
sudo tee /etc/yum.repos.d/ol9-official.repo <<'EOF'
[ol9_baseos_latest]
name=Oracle Linux 9 BaseOS Latest
baseurl=https://yum.oracle.com/repo/OracleLinux/OL9/baseos/latest/$basearch/
gpgkey=https://yum.oracle.com/RPM-GPG-KEY-oracle-ol9
gpgcheck=1
enabled=1

[ol9_appstream]
name=Oracle Linux 9 AppStream
baseurl=https://yum.oracle.com/repo/OracleLinux/OL9/appstream/$basearch/
gpgkey=https://yum.oracle.com/RPM-GPG-KEY-oracle-ol9
gpgcheck=1
enabled=1
EOF
```

这个配置的 `baseurl` 与 Oracle 官方文档及社区常用的 Oracle Linux 9 源地址一致。

### 第三步：刷新缓存并验证

```bash
sudo dnf clean all
sudo dnf makecache
```

这次应该不会再卡死了。如果出现 SSL 证书相关的报错（部分环境遇到过 `Curl error (60)`），可以临时加上 `--setopt=sslverify=false` 测试是否为证书问题。但正常情况下，官方源配合系统自带的 CA 证书应该能直接工作。

### 后续建议

先确认 `dnf makecache` 能顺利完成。如果之后确实需要 EPEL 等额外仓库，建议用 Oracle 官方提供的 `oracle-epel-release-el9` 包来配置，而不是手动编辑 repo 文件——这样能避免路径和仓库 ID 不匹配的问题再次出现。国内镜像站的 Oracle Linux 9 路径目前看来不太稳定，暂时不要在这上面继续花时间了。





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


