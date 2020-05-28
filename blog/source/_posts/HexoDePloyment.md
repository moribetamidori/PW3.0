---
title: Hexo Deployment | Hexo + Vultr 部署避坑指南
date: 2020-04-20 01:13:35
tags: 
- code
- web
- deployment
---
<b>INTRO:</b>  因为想要的课没选上所以打算写一篇避坑指南增加一些人品。以下是我在部署hexo博客到vultr服务器的时候遇到的一些bug和解决方案。希望能够有所帮助。系统用的是Vultr服务器的Centos 8, 如果用的是Ubuntu的话一些指令的前缀有可能会有些不一样，此时启动谷歌即可。可能会夹杂一些中二词汇，尽请谅解，嗝。

<b>安装依赖(总之安装就完事了):</b> 

```
$ yum install -y gcc gcc-c++ make git openssl
$ yum install -y nginx
$ yum update and yum install -y nodejs
$ systemctl enable nginx and systemctl start nginx
```

<b>创建超级用户:</b> 

```
# 在root用户下新建一个用户名
root@server:~# adduser 「username」
# 给新建的用户名设置密码
root@server:~# passwd 「password」
# 将新建的用户加入超级权限组
root@server:~# usermod -aG wheel 「username」
# 将新建的用户加入nginx组
root@server:~# usermod -aG nginx vultr
```
<b>把push到github上的hexo repo·初代载到本地:</b> 
- 这里假设你已经在本地localhost:4000把初代hexo调试好了并传到了github上。
- 此时非常容易出一个和theme有关的错误(请看下文常见错误4)。
- 为了省事直接cd进你新建的用户然后git clone 即可。


<b>新建git裸仓库(bare repository)存放本地Hexo:</b> 
- Note: 搞了这玩意就可以在本地电脑一键更新博客，非常有用。
```
# cd进你的用户名目录
cd /home/username 
# 使用以下命令创建git裸仓库，可以使用任何名称，在此使用blog.git
git init --bare blog.git
# 在blog.git/hooks里vim一个post-receive文件
vim ~/blog.git/hooks/post-receive
```
    然后在post-receive里输入以下内容，自行填充「」中的内容。分别是第一行和第三行。
```
GIT_REPO=/home/「username」/blog.git
TMP_GIT_CLONE=/tmp/blog
PUBLIC_WWW=「你网站的根目录path」
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```

<b>配置nginx:</b> 
1. 先cd进nginx配置目录，path通常是 /etc/nginx
2. 备份一下nginx.conf
```
cp nginx.conf nginx.conf.bak
```
3. vim 进nginx.conf 
    - 以防万一先把user 旁边的nginx改成root
4. 滚到server那一块，把server_name改成你买好的域名(这里同时假设你已经把你域名指向这个服务器了)
5. 往下一行root那儿，把path改成你博客的根目录。
6. 最后使用:wq保存退出，如果无法write的话估计又是权限问题，此时使用sudo vim nginx.conf 即可。
7. 最后搞完后用 sudo nginx -t 查看配置是否成功。至于为什么用sudo呢，因为有时候不用就会报错用就不会。我也不知道为什么。
8. 记得每次更改_config.yml的内容后systemctl restart nginx 一下。

<font color=#1E90FF><i>查看nginx error log的小Tip:</i></font>
1. 首先error log藏着的地方非常隐蔽，一般cd /var/log/nginx 即可。
2. 但是此时你通常可能因为权限原因还是不能vim进error.log里，这时被挨打过几百次的你无师自通了，sudo su后再vim进去即可。

<b>将本地hexo指向服务器</b> 
- 搞完这步就可以真的在本地一键部署了
1. 找到你根目录的_config.tml 文件
2. 在deploy下输入以下配置，自行换username:
```
deploy:
type: git
repo: 「username」@服务器ip:/home/「username」/blog.git
branch: master
```
3. 最后在本地终端输入hexo deploy即可一键部署，记得部署前hexo g一下。

<b>常见错误:</b> 

<font color=#FF1493	><b>Error1: 非超级用户无法执行命令</b></font>
```
# Error: This command has to be run under the root user.
# 解决方法:
username@server:~# sudo su
[sudo] password for「username」: 
# 在上面冒号后输入你之前设置的密码，然后就会切换到root。此时再跑一遍command即可。
```
<font color=#FF1493	><b>Error2: nginx没问题Ping也没问题hexo部署也显示成功了就但网页就是加载不出来</b></font>
- 大概率是防火墙的问题
解决方法: 
1. Run 以下代码: sudo firewall-cmd --state
2. 如果显示running则disable firewall: sudo systemctl stop firewalld
3. 如果想永久disable则看这个<a  href = https://linuxize.com/post/how-to-stop-and-disable-firewalld-on-centos-7/>帖子</a>即可。

<font color=#FF1493	><b>Error3: Permission Denied - 各种权限问题导致网页403/forbidden (详见nginx的error.log)</b></font>
解决方法:
1. Run以下代码更改所有文件权限至0640: sudo find 「你博客根目录的path」 -type f -print0 | xargs -0 chmod 640
2. 有可能是nginx.conf的用户权限问题，如果还没把user从nginx改成root的话请立刻改。
3. 缺少index.html 文件，如果没有直接从本地部署的话，可能需要通过运行hexo g来创建index.html界面。
4. 可以试试将 vim进 /etc/selinux/config 里将SELINUX=enforcing改成SELINUX=disabled后重启服务器。(重启后防火墙如果没有设置成permanently disabled的话可能还需要再stop一次。以及always systemctl restart nginx。)

<font color=#FF1493	><b>Error4 整个theme 在github上gray out了。</b></font>
- 好不容易在把下载的模板修修改改成自己喜欢的样子后发现整个theme folder在传到github上后完全点不开，也就是没传上去。
解决方法:
1. 搞个新的repo，把.gitignore删了，然后cd 进 theme folder，启动rm -rf .git(永久删除theme里有可能会自带的.git文件)，再上传即可。
