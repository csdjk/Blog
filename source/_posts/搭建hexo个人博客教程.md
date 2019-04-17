---
title: 搭建hexo个人博客教程
top: 999
categories: 
- Hexo
tags:
- Hexo
- linux
- git
- 云服务器
---
# hexo+git+nginx在云服务器搭建hexo博客教程


###  本地(win10),云服务器(ubuntu 16.04)

## 一: 本地环境配置: 
<!--more-->

#### 1. 配置node环境

[node官网下载](https://note.youdao.com/)


安装完成后: win + R键打开运行窗口 输入cmd 进入命令行窗口
输入: 

```
node -v
```
出现版本号则代表成功
<!-- ![在这里插入图片描述]("1.jpg") -->
![]("1.png")

如果出现:<html style="color:red">
'node' 不是内部或外部命令，也不是可运行的程序
或批处理文件。</html>
就需要配置一下环境变量了:
“我的电脑”-右键-“属性”-“高级系统设置”-“高级”-“环境变量” ->
双击"path" -> 新建 -> 输入node安装路径 
如图:  

![2]("2.png")
![3]("3.png")

#### 2. 安装hexo
打开cmd输入执行如下命令:

```
npm install -g hexo-cli
```
安装完成后执行如下命令查看版本:

```
hexo -v
```
![]("4.png")


初始化hexo博客执行如下命令

```
hexo init myblog && cd myblog
npm install
```

初始化完成后配置hexo

主题可以自己去[官网下载](https://hexo.io/themes/)

打开myblog文件夹下的package.json文件添加如下代码:

![]("5.png")

并执行如下命令:
```
npm start
```

然后打开[http://127.0.0.1:4000](http://127.0.0.1:4000)查看一下博客吧

#### 3. git环境搭建

##### git安装: 
[git 官网下载](https://gitforwindows.org/)

生成ssh执行以下命令:
```
git config --global user.name "你的用户名"
git config --global user.email 你的邮箱
ssh-keygen -t rsa -C "你的邮箱"
git config --global core.autocrlf false //禁用自动转换，这个不设置后面上传时会出现警告
```
最后获取到的ssh公钥和密钥在: 

<html style="color:red">
C:\Users\Administrator\.ssh 
</html>

![]("6.png")

## 二: 云服务器环境(ubuntu 16.04)配置: 

### 1. 搭建Git服务器

##### 首先执行以下命令查看是否安装git

```cmd
git --version
//如果未安装执行以下命令
sudo apt-get install git
```

##### 单独创建一个用户来管理git


```cmd
adduser git 
passwd git // 设置密码
su git // 切换用户
cd /home/git/
mkdir -p projects/blog // 创建文件来放置hexo静态工程
mkdir repos && cd repos //创建文件放置git仓库
git init --bare blog.git // 创建一个裸露的仓库
cd blog.git/hooks
vi post-receive //创建hook钩子函数(git提交时自动部署)

hook钩子函数内容:
#!/bin/sh
git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git checkout -f
```

###### 修改权限

```
chmod +x post-receive
exit // 退出到 root 登录
chown -R git:git /home/git/repos/blog.git // 添加权限
```

#### git搭建完成
执行以下命令测试是否能克隆下来

```
git clone git@server_ip:/home/git/repos/blog.git
```
成功后如图:

![]("7.png")


#### 建立ssh信任免密登录 :

1) 创建authorized_keys文件:

```
cd /home/git/.ssh
touch authorized_keys  //存放客户端的ssh公钥(id_rsa.pub)
chmod 600 authorized_keys   //配置权限
```

在本地电脑安装winscp 连接远程服务器如图:

![]("8.png")



#### 把本地的 .ssh/id_rsa.pub  里的内容复制到 服务器  .ssh/authorized_keys文件里


##### 本地测试能否登录

```
ssh git@server_ip   //此时不需要密码, 如果需要密码就有问题仔细检查一下
```

##### 最后限制git用户登录权限, 只能clone, push;

```
cat /etc/shells // 查看`git-shell`是否在登录方式里面，有则跳过
which git-shell // 查看是否安装 
vi /etc/shells
添加(which git-shell)显示出来的路劲，通常在 /usr/bin/git-shell
```

修改/etc/passwd中的权限

```
将原来的
git:x:1000:1000:,,,:/home/git:/bin/bash //原来的
修改为:
git:x:1000:1000:,,,:/home/git:/usr/bin/git-shell //修改后的
```

### 2. 配置nginx服务器

#### 下载安装nginx

```linux
cd /usr/local/src
wget http://nginx.org/download/nginx-1.15.2.tar.gz
tar xzvf nginx-1.15.2.tar.gz
cd nginx-1.15.2
./configure // 如果后面还想要配置 SSL 协议，就执行后面一句！
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-file-aio --with-http_realip_module
make && make install
alias nginx='/usr/local/nginx/sbin/nginx' // 为nginx取别名，后面可直接用
```


##### linux 安装nginx出错时可以参考以下链接:
[linux安装nginx报错](https://blog.csdn.net/nyist327/article/details/41278825)



#### 修改nginx配置文件:

```
cd /usr/local/nginx/conf
vi nginx.conf
修改 root 路径为: /home/git/projects/blog; 
同时将 user 改为 root
```
![]("9.png")

### 3. hexo _config.yml文件配置: 

<html style='color:red'>
配置_config.yml的deploy属性:
</html>

```
deploy:
  type: git
  repo: git@serverId:/home/git/repos/blog.git
  branch: master
```
![]("10.png")

最后 cmd 运行上传至服务器...

```
npm run deploy
```

# END !




