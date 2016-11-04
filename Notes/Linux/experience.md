# Linux 下的一些使用经验

- 删除开头带有横杠的文件，如：-name(文件名)

`$sudo rm -rf -- -name`

- 删除带有其他特殊字符的文件：

```
$sudo rm -rf \特殊字符
$sudo rm -rf \\
$sudo rm -rf \>
$sudo rm -rf "特殊字符"
$sudo rm -rf "*"
$sudo rm -rf '\'
```

- 利用 grep 和 sed 组合，只返回 ip 地址：

```
$ifconfig eth1 | grep "inet addr" | sed 's/^[^:]*:\([^ ]*\).*/\1/g'
192.168.56.105
# 只返回 eth1 的 ip 地址
```

- 使用 jq，只显示 docker ip：

```
$sudo docker inspect CONTAINER_NAME | jq .[0].NetworkSettings.IPAddress
"172.17.0.2"
$sudo docker inspect CONTAINER_NAME | jq .[0].NetworkSettings.Gateway
"172.17.0.1"
# 显示 docker 容器的网关地址
```

- 安装更新时，出现 `W: GPG error: http://ppa.launchpad.net trusty InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 93C4A3FD7BB9C367` 错误：

```
$sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
$sudo apt-get clean
$sudo apt-get update
```

- 将指定的 IP 从 known_hosts 中删除

`$ssh-keygen -f /home/xxoo/.ssh/known_hosts -R 172.17.0.2`

- 查看端口信息

`$netstat -natp`

- ssh ubuntu系统，一直出现 Permission denied, please try again. 时，只要修改 /etc/ssh/sshd_config 文件就行

```
$sudo vim /etc/ssh/sshd_config
 PermitRootLogin yes  //添加在任意位置
 #PermitRootLogin without-password  //注释这句
```

- 将普通用户添加到sudo组

```
#visudo
root    ALL=(ALL:ALL) ALL
NAME    ALL=(ALL:ALL) ALL  //添加
```

- 将普通用户添加到sudo组后，不能使用 TAB

```
$sudo vim /etc/default/useradd
SHELL = /bin/sh
改为
SHELL = /bin/bash
```

- 查看cpu的信息

`$lscpu`

- 查看打开端口的程序

`$lsof -Pan -i tcp -i udp`

- 进程树

`$pstree -p`

- 显示/usr/bin 目录下的文件的用途

`$ls /usr/bin | xargs whatis | grep -v nothing | less`

- 列出大文件和目录

`$du -h | grep -P "^\S*G"`

- 查看指定文件夹大小

`$du -sh DIC_NAME`

- 获取 IP 地址

`$ip -o -4 addr show |awk -F '[ /]' '/global/ {print $4}'`

- 按cpu利用率ps 排序

`$ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu | sed "/^ 0.0 /d"`

- 比较两个目录下的同名文件

`$diff {$path 1,$path2}/file_to_diff`

- 从文件中提取ip 地址

`$grep -Eo \([0-9]\{1,3\}[\.]\)\{3\}[0-9] file_name | sort | uniq`

- shell 下使用python 打印 HOSTNAME

`$python -c "import platform; print platform.node()"`

- 权限进制之间的转换

> r=4，w=2，x=1

- scp 断点续传

`$rsync -P --rsh=ssh home.tar 192.168.0.34:/home/home.tar`

- 后台运行

```
在shell命令行下挂起程序 : Ctrl-z
$bg BACKSTAGE
```

- 查看后台运行的程序

`$jobs`

- 将后台程序放到前台执行

`$fg BACKSTAGE`

- 将程序添加到启动器

```
$sudo vim /usr/share/applications/Pycharm.desktop
[Desktop Entry]
Type=Application
Name=Pycharm
GenericName=Pycharm
Comment=Pycharm:The Python IDE
Exec="/xxx/pycharm-community-3.4.1/bin/pycharm.sh" %f
Icon=/xxx/pycharm-community-3.4.1/bin/pycharm.png
Terminal=pycharm
Categories=Pycharm;
```

- 本地文件夹同步

`$rsync -auvrtzopgP --progress /root/ /tmp/root_back/`

// 删除root 下的文件不会同步删除/tmp/root_back/，除非加入 `--delete` 选项

- Linux 下载百度云资源的方法

    1. 首先将资源分享，获取分享地址，也就是引用地址：`https://pan.baidu.com/s/1jIdQl1s`
    2. 打开链接地址，点击下载，然后获取真实下载地址：`https://yqall02.baidupcs.com/file/90b77389ef007bb316c3619859782ba1?bkt=p3-0000adf8acd445d53d7741b4635013ba5f26&fid=3224872156-250528-515535277269379&time=1478243836&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-DJoo1vqy2838c%2FaITqUuxHQ3eRY%3D&to=yqhb&fm=Yan,B,T,t&sta_dx=3654446542&sta_cs=2&sta_ft=zip&sta_ct=3&sta_mt=3&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=0000adf8acd445d53d7741b4635013ba5f26&sl=75235406&expires=8h&rt=sh&r=225481680&mlogid=7158163452737988653&vuk=3224872156&vbdid=2805772048&fin=DockerImage.zip&fn=DockerImage.zip&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=7158163452737988653&dp-callid=0.1.1&hps=1&csl=163&csign=q%2BqeoX3kCFkwRC5kNEIf%2Ftl53ew%3D`
    3. 使用wget 下载 `wget -c --referer=引用地址 -O 文件名 "真实下载地址"`
    ```
    $wget -c --referer=https://pan.baidu.com/s/1jIdQl1s -O FILE_NAME "https://yqall02.baidupcs.com/file/90b77389ef007bb316c3619859782ba1?bkt=p3-0000adf8acd445d53d7741b4635013ba5f26&fid=3224872156-250528-515535277269379&time=1478243836&sign=FDTAXGERLBH-DCb740ccc5511e5e8fedcff06b081203-DJoo1vqy2838c%2FaITqUuxHQ3eRY%3D&to=yqhb&fm=Yan,B,T,t&sta_dx=3654446542&sta_cs=2&sta_ft=zip&sta_ct=3&sta_mt=3&fm2=Yangquan,B,T,t&newver=1&newfm=1&secfm=1&flow_ver=3&pkey=0000adf8acd445d53d7741b4635013ba5f26&sl=75235406&expires=8h&rt=sh&r=225481680&mlogid=7158163452737988653&vuk=3224872156&vbdid=2805772048&fin=DockerImage.zip&fn=DockerImage.zip&slt=pm&uta=0&rtype=1&iv=0&isw=0&dp-logid=7158163452737988653&dp-callid=0.1.1&hps=1&csl=163&csign=q%2BqeoX3kCFkwRC5kNEIf%2Ftl53ew%3D"
    ```