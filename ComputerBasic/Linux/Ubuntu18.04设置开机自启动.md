{% note info %}

#### Note1:

由于新版ubuntu不再使用**initd**管理系统，而改用**systemd**;

故用systemctl命令来替换了**service**和**chkconfig**的功能

系统开机启动，systemd默认读取`/etc/systemd/system`下的配置/该目录下的文件会链接`lib/systemd/system/`下的文件

{% endnote %}

{% note info %}

#### Note2:

sh的脚本，脚本开头的#!+shell，这里的shell一般有sh和bash，而sh其实是‘bash --posix’的变体，二者是一样的，这么看来，sh其实是bash的一种标准:flushed:

{% endnote %}

<hr>

## 开始配置

> 我的自定义启动文件是rc-local.service，直接修改它

### 配置rc-local.service

```sh
#  SPDX-License-Identifier: LGPL-2.1+
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

# This unit gets pulled automatically into multi-user.target by
# systemd-rc-local-generator if /etc/rc.local is executable.
[Unit]
Description=/etc/rc.local Compatibility
Documentation=man:systemd-rc-local-generator(8)
ConditionFileIsExecutable=/etc/rc.local
After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
RemainAfterExit=yes
GuessMainPID=no

#这里是需要添加的部分
[Install]
WantedBy=multi-user.target
Alias=rc-local.service

```

一般正常的启动文件主要分成三部分:sweat_drops:

*[Unit]* 段: {% label info@启动顺序与依赖关系 %}

*[Service]* 段: {% label warning@启动行为,如何启动,启动类型 %}

*[Install]* 段: 定义如何安装这个配置文件，即{% label success@怎样做到开机启动 %}

### 配置rc.local

Ubuntu18.04默认没有`/etc/rc.local`这个文件，需要自己创建

如下:point_down:：

```sh
# !/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

echo "this just a test" > /home/luoj/testdir/text.txt

exit 0
```

<hr>

## 设置开机自启动

```sh
#添加可执行权限
sudo chmod +x /etc/rc.local
#添加rc-local.service软连接到/etc/systemd/system
sudo systemctl enable rc-local
#启动这个服务
sudo systemctl start rc-local.service
#查看这个服务
sudo systemctl status rc-local.service
```

