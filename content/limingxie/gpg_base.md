---
title: "Mac 安装和简单的使用GunPG（GPG） "
date: 2120-06-04T20:38:02+08:00
draft: true
user: 李明燮
---

Gnu Privacy Guard 是一种加密的软件？
具体GPG是什么？ PGP是什么？ 可以百度谷歌一下...^^

安装
```
$ brew install gpg
==> Installing dependencies for gnupg: npth, libtasn1, gmp, nettle, libunistring, libffi, p11-kit, gnutls, libgpg-error, libgcrypt, libksba, libassuan, pinentry, gettext, adns, libusb
==> Installing gnupg dependency: npth
... ... ... ...
`Error`: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink share/man/man8/addgnupghome.8
/usr/local/share/man/man8 is not writable.
... ... ... ...
```
哎？ 怎么有个Error?
```
$ cd /usr/local/share/man/man8
$ ls -l
total 104
... ... ... ...
drwxr-xr-x     7 root       admin    224  1 24 14:40 man8
... ... ... ...
```
原来没有写入权限, 添加写入权限。
```
sudo chmod 775 man8
```
好了，在运行下面的命令
```
$ brew link gnupg
Linking /usr/local/Cellar/gnupg/2.2.7... 68 symlinks created
```
看看有没有正常安装
```
$ gpg --help
gpg (GnuPG) 2.2.7
libgcrypt 1.8.2
... ... ... ...
```
配置公钥和私钥

gpg --full-generate-key

```
$ gpg --full-generate-key
gpg (GnuPG) 2.2.7; Copyright (C) 2018 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

请选择您要使用的密钥种类：
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (仅用于签名)
   (4) RSA (仅用于签名)
您的选择？ 1
RSA 密钥长度应在 1024 位与 4096 位之间。
您想要用多大的密钥尺寸？(2048)
您所要求的密钥尺寸是 2048 位
请设定这把密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0)
密钥永远不会过期
以上正确吗？(y/n)y
密钥的有效期限是？(0)
密钥永远不会过期
以上正确吗？(y/n)y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

真实姓名：Li MingXie
电子邮件地址：li_mingxie@163.com
注释：nihao
您选定了这个用户标识：
    “Li MingXie (nihao) <li_mingxie@163.com>”

更改姓名(N)、注释(C)、电子邮件地址(E)或确定(O)/退出(Q)？O
我们需要生成大量的随机字节。这个时候您可以多做些琐事(像是敲打键盘、移动
鼠标、读写硬盘之类的)，这会让随机数字发生器有更好的机会获得足够的熵数。
我们需要生成大量的随机字节。这个时候您可以多做些琐事(像是敲打键盘、移动
鼠标、读写硬盘之类的)，这会让随机数字发生器有更好的机会获得足够的熵数。
gpg: /Users/limingxie/.gnupg/trustdb.gpg：建立了信任度数据库
gpg: 密钥 xxxxx 被标记为绝对信任
gpg: directory '/Users/limingxie/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/Users/limingxie/.gnupg/openpgp-revocs.d/xxxxx.rev'
公钥和私钥已经生成并经签名。
```

用以下命令可以查看配置好的公钥和私钥。

gpg --list-keys：查看所有keys  
gpg --list-public-keys：查看所有公钥  
gpg --list-secret-keys：查看所有私钥  

gpg --armor --export <你的邮箱>：查看公钥  
gpg --armor --export-secret-keys <你的邮箱>：查看私钥  

gpg --list-public-keys --keyid-format long：查看公钥的完整摘要信息  
gpg --list-secret-keys --keyid-format long：查看私钥的完整摘要信息  


参考文章：
[Gnu 隐私卫士 (GnuPG) 袖珍 HOWTO (中文版)](https://www.gnupg.org/howtos/zh/index.html)
[数字签名是什么？](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)