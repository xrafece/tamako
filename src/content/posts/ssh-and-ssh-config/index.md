---
title: SSH and SSH Config
published: 2025-05-09
description: 'What is SSH ? How we configuration the SSH service ? '
# image: 'https://upload.wikimedia.org/wikipedia/commons/0/00/Unofficial_SSH_Logo.svg'
image: './cover.webp'
tags: [Linux, SSH]
category: 'Linux'
draft: false 
lang: ''
---


<iframe width="100%" height="468" src="https://www.youtube.com/embed/YS5Zh7KExvE?si=L6s4e5q10To_34Tu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## What is OpenSSH

* SSH is Secure Shell Protoctl 安全外壳协议
* SSH default prot `22`
* OpenSSH is a remote management tool, that gives you access to run commands on another machine.

### First of all

#### Install

On Ubuntu/Debian `apt`

```shell
apt install ssh
```

But in many cases, ssh and sshd is already installed.

#### Then, Start

Start ssh service on systemd. (as server)

```shell
systemctl start ssh/sshd
```

> Ssh and sshd is different. Ssh is the client, sshd is the server.
>
> In some Linux distributions, the ssh service in systemd is called ssh, and in others it is called sshd, but they actually refer to the daemon process on the ssh server side.
>
> 在一些 linux 发行版本中，ssh 服务叫做 ssh，一些叫做 sshd，但是他们其实指的都是 ssh 服务端的守护进程。

#### Basic Use

```shell
ssh user@ip
```

Such As:

```shell
ssh root@192.168.100.1
```

By default, use password to login.

When first connect to new server.  ssh will recommind you whether save key fingerprint.

```shell
 ssh root@192.168.100.72
 
The authenticity of host '192.168.100.72 (192.168.100.72)' can't be established.
ED25519 key fingerprint is SHA256:iRUO3p8br5Jhcsd6LkR2pD1W2OxmuoSkALHIFloc8OU.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:13: 192.168.100.12
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.100.72' (ED25519) to the list of known hosts.
root@192.168.100.72's password: 
```

Then 192.168.100.71 and it's fingerprint will be save in the `known_hosts` file. When connect to this ip address again, ssh will auto search in know_hosts file.

##### What is `known_hosts` file ?

Every time when we connect to a server, it's going to ask me the first time "Are you sure you want to connect is going to actually show me the fingerprint. And if I say yes, it's going to save the fingerprint in this file.

So that way it's not going to ask me again the next time we go to connect.

每次链接新服务器时，都会询问是否保存指纹。如果保存，指纹会保存在这个文件中，并且下次登录时，他将不再询问。

它的作用就是为了安全考虑，当同一个ip地址的指纹发生改变时，`ssh` 会提醒你对应ip的指纹已经发生改变，这可以防止你在不知情的情况下出现安全问题。

> Once the client verifies and accepts the fingerprint, it is stored in the `~/.ssh/known_hosts` file and will be automatically verified when used again.
> 一旦客户端验证并接受指纹，它就会存储在 `~/.ssh/known_hosts` 文件中，并在再次使用时自动进行验证。

## SSH Config

 Ssh config file is in `~/.ssh/config` ,the `~` is `$HOME` directory. So the file location can also be represented by  `$HOME/.ssh/config`.

`.ssh` directory :

```shell
ls -lAh
total 56
-rw-r--r--  1 xrafece  staff   151B Dec 29 19:22 config
-rw-------  1 xrafece  staff   464B Jun 24  2023 id_ed25519_github_xrafece
-rw-r--r--  1 xrafece  staff    98B Jun 24  2023 id_ed25519_github_xrafece.pub
-rw-------  1 xrafece  staff   3.9K Apr 29 10:10 known_hosts
-rw-------  1 xrafece  staff   2.7K Apr 24 16:33 known_hosts.old
-rw-------  1 xrafece  staff   432B Jan 11  2022 podman-machine-default
-rw-r--r--  1 xrafece  staff   116B Jan 11  2022 podman-machine-default.pub
```

And the config file :

```config
 Host myserver            # ssh server name whatever you want
   Hostname 10.211.55.5   # ip address or domain
   Port 22
   User root              # login user
```

Then we can use  `ssh myserver`  to connect `10.211.55.5` use prot `22` and user `root`

> If user is root, on server, we need to change sshd config in `/etc/ssh/sshd_config` .
>
> Change `#PermitRootLogin prohibit-password`  to `PermitRootLogin yes` .
>
> This means permit login ssh on root user.
>
> 这表示允许使用 root 用户登录，原来的 `prohibit-password` 表示不允许使用密码登录 root 用户
>
> When we change ssh config, don't forget ***restart*** ssh/sshd service.

We can add any amount you want this config item. Such as :

```config
Host myserver
  Hostname 10.211.55.5
  Port 22
  User root
  
Host myserver2           # name unique
  Hostname 10.211.55.6
  Port 22
  User xrafece
```

## Using SSH Keys

Using public/private keys to log in ssh.

This is important, because it allows we even greater security and also gives we additional convenience.

In th `$HOME/.ssh` directory shown above, there are several files that need attention. Such as `id_ed25519_xxxxxxxxxx` and `id_ed25519_xxxxxxxxxx.pub` .  The `ed25519`  is tht encryption algorithms. First file is the private key, secondly is the public key that name same as first file and end of `.pub`.

### Generate Keys

Use `ssh-keygen` by itself to generate a brand new key.

```shell
ssh-keygen 

Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/xrafece/.ssh/id_ed25519): /Users/xrafece/.ssh/id_ed25519_pd_ubuntu
Enter passphrase for "/Users/xrafece/.ssh/id_ed25519_pd_ubuntu" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/xrafece/.ssh/id_ed25519_pd_ubuntu
Your public key has been saved in /Users/xrafece/.ssh/id_ed25519_pd_ubuntu.pub
The key fingerprint is:
SHA256:NOGwgG2OejvymVtBRfYp5EOZgv9aGIPqHXQ1TS06hbA xrafece@Xrafece-MacBook-Air.local
The key's randomart image is:
+--[ED25519 256]--+
|   +.oOo=..      |
|  o +B+O * .     |
|   *.E* X .      |
|  oo=. * .       |
| o. o=  S        |
|o ....o          |
|....oo           |
|..o=.            |
| o=o             |
+----[SHA256]-----+
```

In this command :

The first question is where do we save the key, just press enter to save default location, we need to make sure that we don't already have a key with the same name before. If we do it's going to be overwritten which could be very bad, because if that key maybe was our only way into a server.

The second question is the key passphrase. We can enter the passphrase and again. Now what a passphare is an added layer of security that makes it so that you need to know that passphrase to begin using that key.

第一个输入的是生成密钥保存的位置，我们要确保没有同名密钥被覆盖。第二个要输入的内容是密钥的密码，该密码保存在本地，它是密钥的又一层安全措施，当密钥被窃取时需要额外输入密码才可以使用。

Then we see `~/.ssh` directory again:

```shell
ls -lAh
total 88
-rw-r--r--  1 xrafece  staff   210B Apr 29 20:00 config
-rw-------  1 xrafece  staff   464B Jun 24  2023 id_ed25519_github_xrafece
-rw-r--r--  1 xrafece  staff    98B Jun 24  2023 id_ed25519_github_xrafece.pub
-rw-------  1 xrafece  staff   484B Apr 29 21:08 id_ed25519_pd_ubuntu
-rw-r--r--  1 xrafece  staff   115B Apr 29 21:08 id_ed25519_pd_ubuntu.pub
-rw-------  1 xrafece  staff   4.7K Apr 29 20:13 known_hosts
-rw-------  1 xrafece  staff   4.0K Apr 29 20:02 known_hosts.old
-rw-------  1 xrafece  staff   432B Jan 11  2022 podman-machine-default
-rw-r--r--  1 xrafece  staff   116B Jan 11  2022 podman-machine-default.pub
```

We have this file here those two files (`id_ed25519_pd_ubuntu` and `id_ed25519_pd_ubuntu.pub`) did not exist.

```shell
ssh-keygen -C "comment a message such as name, email" -t rsa
```

* `-C` 添加注释，可以随意填写，默认使用 `user@computer name` 建议填写该 ssh 的用途以方便后期进行管理
* `-t` means type of key, the possible values are `ecdsa`, `ecdsa-sk`, `ed25519` (the default), `ed25519-sk`, or `rsa`.  密钥类型（加密算法）可选值从以上几个中选择。
  * `ed25519` 相比 `rsa` 拥有更短的公钥，更安全的算法，更推荐使用。
* Name of keys：It is recommended to use `xxxx_id_type` or `id_type_xxxx` as the key name, which is convenient for checking the key type and purpose.

> More option here: <https://linux.die.net/man/1/ssh-keygen>

#### Use Keys

Then we copy this public key to our server path `$HOME/.ssh/authorized_keys` file in a new line.

Each line in this file is the public key of a key.

```shell
cat id_ed25519_pd_ubuntu.pub
```

```shell
ssh root@10.211.55.5
```

```shell
vim $HOME/.ssh/authorized_keys
```

For me, I use pbcopy comand on my mac :

```shell
cat id_ed25519_pd_ubuntu.pub | pbcopy
ssh myserver
cd
vim .ssh/
```

Then we need change ssh config, add `IdentifyFile` to `myserver` item :

```config
Host myserver
  Hostname 10.211.55.5
  Port 22
  User root
  IdentityFile ~/.ssh/id_ed25519_pd_ubuntu
```

> If you use default name of ssh key, this step can be omitted, but it is not recommended because the default name is not easy to manage and maintain.
>
> 如果使用默认名称，比如 `id_rsa` `id_ed25519` 等，这些会包含在默认配置中，所以不再需要修改配置文件指定密钥文件，但是不建议使用这种方式，这样不方便后期对密钥进行管理，除非你只需要一个密钥。

##### More Better Way

We can use command `ssh-copy-id`  copy the public key easier.

```shell
ssh-copy-id -i ./id_ed25519_pd_ubuntu.pub myserver
```

* `-i` specify the key file name
* `myserver` can also use `root@10.211.55.5`

Enter the ssh server user password after the command, and it will copy the specified public key to the `authorized_keys` file in the ssh server. Then when you connect to the ssh server, you will use the public key to log in (of course, you need to configure the local ssh config file).

在命令后输入 ssh 服务器用户密码，它会将指定的公钥复制到 ssh 服务器的 `authorized_keys` 文件中。然后在连接 ssh 服务器就会使用公钥登录（当然需要配置好本地 ssh config 文件）。

##### About keys

SSH achieves secure transmission through asymmetric encryption. As its named, the public key can be public anywhere.  However, the private key needs to be kept properly. If the private key is leaked, it will be very dangerous to the SSH server. This is also the purpose of adding passphrase operations to the private key to ensure that the private key is not leaked as much as possible.

ssh 通过不对称加密实现安全传输，公钥如其名称一样，是可以随意公开的。但是私钥需要妥善保管，如果私钥泄露，对 ssh 服务器是很危险。这也是对私钥增加密码操作的目的，尽可能保证私钥不被泄露。

##### Use ssh agent

SSH agent will allow you to add the key in memory, and this is especially useful when we connect a server many times.

First of all, check if it's running.

```shell
ps aux | grep ssh-agent
xrafece          76578   0.0  0.0 426845968    736   ??  S    Thu04PM   0:00.28 /usr/bin/ssh-agent -l
xrafece          70313   0.0  0.0 410059264    144 s000  R+   11:24AM   0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox ssh-agent
```

First line means ssh-agent is running on my machine. **If not** , run this to start ssh agent :

```shell
ssh-agent
```

Then run :

```shell
ssh-add ./id_ed25519_pd_ubuntu
```

Mention that this is a **private key**, not a public key. If we have set a passphrase, enter it next.

Then we access to server directly , unless shut down the ssh-agent.

## Configuring OpenSSH

This is how to configure the ssh server side.

### Manage SSH Service On systemd

Firsr of all, the `status`  is used to view the ssh service status.

```shell
systemctl status ssh

● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-04-30 10:07:34 CST; 5h 25min ago
TriggeredBy: ● ssh.socket
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 76908 (sshd)
      Tasks: 1 (limit: 2206)
     Memory: 3.4M (peak: 19.4M)
        CPU: 593ms
     CGroup: /system.slice/ssh.service
             └─76908 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
```

The `loaded` display enabled which means that it's automatically going to start as soon as the server start. If not, we can use `enable` to support this.

```shell
systemctl enable ssh
```

And the following is to control the start, stop and restart of the ssh service.

```shell
systemctl start ssh
```

```shell
systemctl restart ssh
```

```shell
systemctl stop ssh
```

### Change Config of SSHd

The configuration file is located at `/etc/ssh/sshd_config` .

This also have a file named `/etc/ssh/ssh_config`, **not that**. That is the ssh client config. That is for global client configuration setting accross the entire distribution. When we use the ssh client it's actually going to configure itself along with this config file. Our local ssh config file that above will override that. So now we're not going to focus on that.

```shell
vim /etc/ssh/sshd_config
```

```config
...
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
...
```

We remove the leading '#' sign and change `PasswordAuthentication` value `yes` to `no` which means we can't use password to login this ssh server.

在这里禁用使用密码登录选项，增加 ssh 服务器安全性，当然，前提是我们已经配置好密钥登录。

> Use Command `sshd -T | grep passwordauthentication` to check whether the configuration is effective.
>
>
> `sshd -T` 会显示 SSH 服务的 当前运行时配置。如果它返回 `passwordauthentication yes`，说明：
>
> * 1 `sshd_config` 中的 `PasswordAuthentication` 未被正确设置（可能被注释或覆盖）
> * 2 PAM 模块强制启用了密码认证（如 `/etc/pam.d/sshd` 中有相关配置）
> * 3 其他 SSH 配置文件覆盖了主配置（如 `/etc/ssh/sshd_config.d/*.conf`）

There are many more configurations here that can be modified as needed.  

> See more: <https://linux.die.net/man/5/sshd_config>

## Troubleshotting

When we use ssh or config ssh have some trouble, we can try the following ideas.

* Firewall
* File permissions
* Analyze logs

### Log files

We can take a look at the log files.

The default of ssh connect log is `/var/log/auth.log` ,

we can use `tail -f auth.log` to see what happen when we connect the ssh server.

Maybe you don't see that log file in that path. We technically should be using journalctl to follow log files.

``` shell
journalctl -u ssh -f
```

* `-u` option is the unit name that just means servive deamon what we want.
* `-f` option stands for follow mode

> Video: <https://www.youtube.com/watch?v=YS5Zh7KExvE>
>
> Page: <https://tailscale.com/learn/generate-ssh-keys>
