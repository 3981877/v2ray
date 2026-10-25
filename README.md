# v2ray-centos7使用方法

# v2ray一键脚本
```
bash <(curl -sL https://github.com/3981877/v2ray/releases/download/1.0/v2ray.sh)
```

#### 甲骨云报错无法安装NGINX解决方法：cannot find a valid baseurl for repo:base/7/x86_64

备份现有的YUM配置文件：
```
sudo cp -r /etc/yum.repos.d /etc/yum.repos.d.backup
```

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
```
#### 这一步可以忽略太浪费时间了
```
sudo yum update
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



# CentOS Stream 9 配置阿里云软件源完整指南

## 一、背景说明

CentOS Stream 9 默认的软件源（`mirrorlist.centos.org`）在国内访问速度较慢，甚至可能超时。将软件源替换为阿里云镜像，可以显著提升 `dnf` 安装和更新软件的速度。

你提供的配置片段已经包含了 BaseOS 和 AppStream 两个核心仓库，下面是把它完整落地的操作步骤。

---

## 二、准备工作

### 1. 确认系统版本

先确认你的系统确实是 CentOS Stream 9：

```bash
cat /etc/centos-release
```

预期输出类似：

```
CentOS Stream release 9
```

如果显示的是 CentOS 7 或其他版本，请勿使用本配置。

### 2. 备份现有源文件（推荐）

```bash
sudo mkdir -p /etc/yum.repos.d/backup
sudo mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/
```

> 备份后，如果新源有问题，可以通过 `sudo mv /etc/yum.repos.d/backup/*.repo /etc/yum.repos.d/` 恢复。

---

## 三、写入阿里云源配置

### 1. 创建配置文件

```bash
sudo nano /etc/yum.repos.d/aliyun.repo
```

### 2. 粘贴以下内容

```ini
[baseos]
name=CentOS Stream 9 - BaseOS - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-centosofficial

[appstream]
name=CentOS Stream 9 - AppStream - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos-stream/9-stream/AppStream/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-centosofficial
```

保存退出（`nano` 中按 `Ctrl + O` 保存，`Ctrl + X` 退出）。

### 3. 关于 `$basearch` 变量

`$basearch` 是 DNF 的内置变量，会自动替换为当前系统架构（如 `x86_64`）。无需手动修改。

---

## 四、刷新缓存并验证

### 1. 清理旧缓存

```bash
sudo dnf clean all
```

### 2. 生成新缓存

```bash
sudo dnf makecache
```

如果输出中没有 `404` 或 `Cannot download repomd.xml` 之类的错误，说明源配置成功。

### 3. 验证仓库列表

```bash
sudo dnf repolist
```

预期能看到 `baseos` 和 `appstream` 两个仓库，状态为 `enabled`。

---

## 五、常见问题排查

### 问题 1：`makecache` 报 404

**现象：**

```
Status code: 404 for https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/x86_64/os/repodata/repomd.xml
```

**排查方向：**

- 确认 `baseurl` 路径是否正确。阿里云 CentOS Stream 的路径格式为：
  `https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/x86_64/os/`
- 检查是否误将 `$basearch` 写成了字面量（应保留 `$basearch` 变量）。
- 如果路径确认无误但仍 404，可能是镜像站同步延迟，可稍后重试或临时改用官方源。

### 问题 2：`gpgkey` 下载失败

**现象：**

```
warning: /var/cache/dnf/.../RPM-GPG-KEY-centosofficial: Header V3 RSA/SHA256 Signature, key ID ...: NOKEY
```

**解决方法：**

如果 `gpgkey` 的 HTTPS 地址无法访问，可以将 `gpgkey` 改为本地文件路径（CentOS Stream 9 通常自带该密钥文件）：

```ini
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
```

### 问题 3：提示 “There are no enabled repositories”

**现象：**

```
Error: There are no enabled repositories in "/etc/yum.repos.d"...
```

**原因：** 所有 `.repo` 文件都被移走，且新的 `aliyun.repo` 未成功创建或内容为空。

**解决方法：**

- 确认 `/etc/yum.repos.d/aliyun.repo` 文件存在且内容完整。
- 执行 `ls -l /etc/yum.repos.d/` 检查文件权限和大小。
- 必要时从备份恢复：`sudo mv /etc/yum.repos.d/backup/*.repo /etc/yum.repos.d/`

### 问题 4：DNF 进程被锁

**现象：**

```
Waiting for process with pid 13515 to finish.
```

**解决方法：**

```bash
sudo kill -9 <PID>
sudo rm -f /var/cache/dnf/*.lock
sudo rm -f /var/lib/rpm/.rpm.lock
```

然后重新执行 `sudo dnf clean all && sudo dnf makecache`。

---

## 六、附：完整操作一键脚本（可选）

如果你想一次性完成上述步骤，可以保存以下脚本为 `setup-aliyun-repo.sh`，然后执行 `sudo bash setup-aliyun-repo.sh`：

```bash
#!/bin/bash
set -e

# 备份现有源
mkdir -p /etc/yum.repos.d/backup
mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/ 2>/dev/null || true

# 写入阿里云源
cat > /etc/yum.repos.d/aliyun.repo <<'EOF'
[baseos]
name=CentOS Stream 9 - BaseOS - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos-stream/9-stream/BaseOS/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-centosofficial

[appstream]
name=CentOS Stream 9 - AppStream - mirrors.aliyun.com
baseurl=https://mirrors.aliyun.com/centos-stream/9-stream/AppStream/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=https://mirrors.aliyun.com/centos-stream/RPM-GPG-KEY-centosofficial
EOF

# 刷新缓存
dnf clean all
dnf makecache

echo "阿里云源配置完成。"
```

> 注意：脚本中的 `gpgkey` 如果下载失败，可手动改为 `file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial`。

---

## 七、参考信息

- 阿里云 CentOS Stream 镜像地址：`https://mirrors.aliyun.com/centos-stream/`
- CentOS Stream 9 官方文档：`https://docs.centos.org/`
- DNF 仓库配置说明：`man 5 dnf.conf`
