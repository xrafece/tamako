---
title: Linux Best Practices
published: 2025-05-09
description: 'The best practices of linux'
image: ''
tags: [Linux, SSH]
category: 'Linux'
draft: false 
lang: ''
---



## No.1 创建用户并且配置ssh登录

预设命题：假设你是一个公司技术负责人，你需要给公司新员工开通服务器账户，并配置好 ssh 登录。

1. 给 Linux 服务器创建一个新用户，修改新用户创建模板，给新用户自定义好 oh-my-zsh、vim 常用设置。
2. 给新用户加入指定用户组，授予指定权限（比如 sudo）。
3. 服务器 sshd 配置设置为仅密钥登录，保证服务器安全性
4. 生成新用户密钥，并添加到服务器，完成 ssh 密钥登录配置

实现：

### 自定义新用户主文件模板

Linux 系统中新用户模板位置 `/etc/skel/` 主要目标就是自定义这个目录文件。

```shell
cd /etc/skel/
ls -lAh
```

添加 `.oh-my-zsh` 文件（已经包含了几个常用插件）

复制 `.zshrc` 配置文件，并修改内容

```shell
sudo cp -r /root/.oh-my-zsh/ /etc/skel/.oh-my-zsh
sudo cp /root/.zshrc /etc/skel/.zshrc
sudo vim .zshrc
```

设置所有新用户默认使用 `zsh` 。前提：服务器已经安装 `zsh`

修改 `/etc/default/useradd` 文件配置

```shell
sudo vim /etc/default/useradd
```

找到 `SHELL=` 行，修改为：

```config
SHELL=/bin/zsh
```

### 添加新用户并设置群组和密码

使用 `useradd` 命令添加新用户

```shell
sudo useradd -m 用户名
```

* `-m` 选项会自动创建用户的主目录，（`/home/用户名` ）
* 如果不添加 `-m` ，需要手动创建主目录。

设置新用户密码

```shell
sudo passed 用户名
```

> 直接指定用户组
>
> ```shell
> sudo useradd -m -G 组名 用户名
> ```
>
> 直接指定用户 ID 和主目录
>
> ```shell
> sudo useradd -m -u 1010 -d /自定义路径 用户名
> ```
>
> 直接指定 shell
>
> ```shell
> sudo useradd -m -s /bin/bash 用户名
> ```

验证用户是否创建成功

```shell
id 用户名
grep 用户名 /etc/passwd
ls -lAh /home/用户名
```

给新用户增加 sudo 权限， 添加新用户到 sudo 用户组

```shell
sudo usermod -aG sudo 用户名   # Ubuntu/Debian
sudo usermod -aG wheel 用户名  # CentOS/RHEL
```

* `-aG`：表示追加（`-a`）到指定组（`-G`），避免覆盖现有组。

### 删除用户以及其主目录

```shell
sudo userdel -r 用户名
```

`-r` 选项会删除：

* 用户的主目录，`/home/用户名`
* 用户的邮件目录， `/var/mail/用户名`
* 其他和用户相关的文件，比如cron任务，临时文件

可以添加 `-f` 选项，可以在该用户进程还在运行时强制删除用户，但该进程不会结束，需要 `kill` 掉

### 打开服务器 ssh 配置，禁止密码登录

修改 sshd 配置文件

```shell
sudo vim /etc/ssh/sshd_config
```

```config
...

# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication no

...
```

解除注释，修改配置项为 `no` ，然后重启 `sshd` 服务

```shell
sudo systemctl restart ssh
```

测试配置是否生效

```shell
sudo sshd -T | grep passwordauthentication
```

如果输出为 `no` ， 表示配置已经加载完成，服务器已经禁用密码登录，只能使用密钥登录

> 配置修改保存并重启服务以后，配置依旧未生效，可能原因：
>
> 1. 某些版本 `OpenSSH` 可能存在 `/etc/ssh/sshd_config.d/` 配置文件，
>
> ```shell
> sudo grep -r "PasswordAuthentication" /etc/ssh/sshd_config.d/
> ```
>
> 如果结果为 `yes` 说明这里有配置文件覆盖了之前的配置，修改这里的设置。
>
>
>
> 2. 开启了 `pam` 需要修改 `pam` 配置
>
> 查看 `pam` 中是否有配置接管了 ssh 登录配置
>
> ``` shell
> sudo cat /etc/pam.d/sshd
> ```
>
> 如果存在下面类似行，可能会启用密码认证：
>
> ```config
> auth       required     pam_password.so
> auth       substack     password-auth  # CentOS/RHEL
> auth       include      system-auth    # Ubuntu/Debian
> ```
>
> 检查指向的具体配置文件，修改配置，或者注释这里的行。
>
> 或者在 `/etc/ssh/sshd_config` 中强制禁用 `pam`
>
> ```config
> ...
> UsePAM no
> ...
> ```

### 生成密钥，并上传到服务器目录

新用户使用 `ssh-keygen` 生成密钥

在可以修改 `sshd_config` 的情况下可以直接使用 `ssh-copy-id` 直接自动复制到服务器对应位置。

> `ssh-copy-id` 是一个快捷脚本，可以把本地 public key 复制 `~/.ssh/authorized_keys` 文件中。但是注意，它需要目前有效验证方式登录到服务器，可以是同用户的其他公钥登录，也可以是密码登录。如果是 root 登录，那么它默认会复制到 root 用户的对应文件中，而不是其他用户的对应文件。可以使用 `-t` 修改文件位置。

如果使用 root 用户或者其他账户 通过 `ssh-copy-id` 复制公钥到服务器时，这时候存在一个问题，他会复制到你当前使用的用户的 `$HOME/.ssh/authorized_keys` 文件中，而不是新用户的对应目录，这样新用户依旧无法使用新用户登陆。

可是使用 `-t` 指定目标文件位置，但是主要它需要使用 `-f` 强制复制到服务器指定位置，并且需要修改这个脚本生成的 `.ssh/authorized_keys` 文件和 `.ssh` 文件夹所有权为新用户。

SSH 对 `~/.ssh` 和 `authorized_keys` 的权限要求严格：

* `~/.ssh` 权限必须为 `700`（`drwx------`）
* `authorized_keys` 权限必须为 `600`（`-rw-------`）
* 文件所有权为当前目标用户

#### 我的实践

使用其他账户登录 ssh 服务器

```shell
ssh myserver
```

进入新用户目录，并创建新文件

```shell
cd /home/新用户
mkdir .ssh
touch .ssh/authorized_keys
```

在本机复制密钥

```shell
cat .ssh/xxxxx_id_ed25519.pub | pbcopy
```

然后手动粘贴到服务器新用户

```shell
vim /home/新用户/.ssh/authorized_keys
```

修改 `.ssh` 和 `.ssh/authorized_keys` 文件所有权

```shell
chown -R 新用户:新用户组 /home/新用户/.ssh
```

修改 `.ssh` 和 `.ssh/authorized_keys` 文件权限

```shell
chmod 700 /home/新用户/.ssh
chmod 600 /home/新用户/.ssh/authorized_keys
```

测试ssh是否成功登录。
