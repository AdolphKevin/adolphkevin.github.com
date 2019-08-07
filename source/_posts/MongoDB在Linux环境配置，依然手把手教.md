![](http://ww1.sinaimg.cn/large/007OROpSgy1g5rrq1ld5oj30gx0cxae1.jpg)

说实话，本是不想写这种基础篇的，但是我配置的时候网上教程虽然一大把，但大多数都是缺胳膊少腿的，或者描述不详细的，在此我就按我的部署经验，手把手的教一遍

如果哪里漏了或者有描述不清楚的地方，可以联系我，这种基础文章我要么不写，既然写了，那都是打算持续更新的，就像之前的基础文章[在VsCode中搭建Go开发环境，手把手教你配置](https://adolphkevin.github.io/2019/05/18/%E5%9C%A8VsCode%E4%B8%AD%E6%90%AD%E5%BB%BAGo%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%EF%BC%8C%E6%89%8B%E6%8A%8A%E6%89%8B%E6%95%99%E4%BD%A0%E9%85%8D%E7%BD%AE/)

**本文基于MongoDB4.0，CentOS环境搭建部署**

这是篇解决问题的，就不废话了，按着步骤来，咱啥也不用想就能部署

## 部署开始

因为考虑到有的公司部署的Linux服务器不能连接外网，这里以FTP形式将压缩包上传到Linux服务器后解压安装

```
tar -xvzf mongodb-linux-x86_64-3.2.10.tgz     //解压
// 此处需ROOT权限
mv mongodb-linux-x86_64-3.2.10 /usr/local/mongodb      //将解压后的文件移动到指定目录并改名
cd /usr/local/mongoDB/    //切换到移动后的mongoDB目录
```

关于可以连接外网安装的话，MongoDB的官网就写的挺不错的，毕竟我也没弄过联网下载，我就不瞎写了

好了，接着说解压后我们的操作

#### 创建data目录
```
cd /usr/local/mongodb/    //切换到mongodb
mkdir data  //创建data目录
mkdir data/db     //创建db 目录，用于存储数据库数据
mkdir log    //创建log日志目录，存储日志数据

```

#### config文件配置

先创建我们的配置文件
```
cd /usr/local/mongodb/    //切换到mongodb
mkdir conf  // 创建config文件的目录

vim conf/mongoDB.conf // 创建并打开conf文件
```

接着填写我们的Conifg文件配置内容
```
# mongodb config file

port=27888  #端口
bind_ip= 127.0.0.1,192.168.105.142 #开启外网访问则为0.0.0.0
dbpath=/usr/local/mongoDB/data/db  #数据库存放
logpath=/usr/local/mongoDB/log/mongodb.log #日志文件存放
fork=true #设置后台运行
# auth=true #开启认证
```
先将auth=true注释，因为新部署的MongoDB还没有创建用户，如果直接开启，将会导致MongoDB无法启动

所以只要若是想要给MongoDB设置访问权限，在设置好账号密码后，将`auth=true`取消注释，重新启用下MongoDB即可

#### profile配置

系统profile文件的配置，这是每装一个软件的必备步骤，在profile文件最后面添加环境变量

```
vim /etc/profile  
  
export MONGODB_HOME=/usr/local/mongodb  
export PATH=$PATH:$MONGODB_HOME/bin  
```

若是不喜欢用`vim`进行文本添加，也可以使用`echo`对文件内容的尾行追加内容
```
echo 'export MONGODB_HOME=/usr/local/mongodb' >> /etc/profile

echo 'export PATH=$PATH:$MONGODB_HOME/bin' >> /etc/profile
```

**上述操作二选一即可**，不过都需要重启系统配置才能生效，记住不要重复执行

```
source /etc/profile
```

到这一步，MongoDB该安装的该配置的都已经弄好了，接下来我们就需要启动我们的MongoDB

#### 启动MongoDB

因为启动参数我们都配置到config中，所以直接以config形式来启动
```
mongod --config ../conf/mongodb.conf
```

## 创建用户并分配权限

在这里我建议使用`Studio 3T`连接我们的MongoDB服务器来创建用户，没什么别的理由，就是简单​罢了

先给你们截图操作一下在`Studio 3T`中我们该怎么设置用户权限

选择要设置权限的库，添加用户

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5rrqcgn4nj30y90kcdie.jpg)

再给用户分配权限

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5rrqldw23j30ck09wt8t.jpg)

非admin库，用户一般只需要分配`readWrite`权限即可

而admin库，我们要留一个管理员账户，分配`root`权限，以备不时之需。但是正常操作的时候，能不使用有`root`权限的用户，就不要使用，不仅容易进行一些危险操作，也会导致生成一些只有`root`用户才能操作的文件。换句话说就是影响数据库的稳定性

好了，我们再来看看用`shell`怎么创建用户，这我就创建一个admin下权限为`root`和`userAdminAnyDatabase`的用户吧
```
db.createUser(
{
	user: "naonao",
	pwd: "123456",
	roles:[
            {role:"root",db:"admin"}
           ,{role:"userAdminAnyDatabase",db:"admin"}
        ]
})
```
可以看到，`roles`里面是一个`Json`数组，所以如果我们只需要一个权限，`roles`数组中的元素保留一个即可

#### 账户密码以及指定端口登陆mongoDB

采用用户登陆方式之前记得先把MongoDB服务关了，把配置文件内的用户验证`auth=true`注释取消

进入`mongoDB/bin`文件路径下
```
cd /usr/mongoDB/bin
```
然后使用账号密码以及指定对应的端口，来启动我们的MongoDB，如果是默认的27017端口，可以不进行指定
```
mongo -u "username" -p "password" -authenticationDatabase "admin" --port 27888
```
账户默认验证的就是admin，所以如果是属于admin数据库的用户，那么把`--authenicationDatabase "admin" `去掉也没关系

如果使用的用户名密码，是属于别的库，那么就必须要指定对应的库咯~把`'admin'`改成登陆用户对应的库即可

## 关闭MongoDB
```
> use admin
> db.shutdownServer()
```
或者直接`kill`对应的Mongo进程

先查询MongoDB的进程
```
ps -aux | grep mongo
```

`killall`MongoDB的所有相关进程
```
killall mongo
```

有时候使用`kill`指定`mongo`对应的`pid`无法结束进程，`killall`执行后`mongo`进程还是依然存在，那么只好拿出杀手锏`killall -9 mongo`

不过`killall -9 `是一个**危险的命令**，这个强大和危险的命令迫使进程在运行时突然终止，进程在结束后不能自我清理。危害是导致系统资源无法正常释放，一般不推荐使用，除非其他办法都无效。 

## Linux开放指定端口

MongoDB在现在已经搭建完成，在Linux上已经可以正常使用咯

我们的MongoDB加了账号密码后若是依然不放心安全性，可以将我们的端口进行变动，不使用默认的端口27017

端口进行改变后，我们要记得在Linux上打开对应的端口，否则客户端是无法连接上我们的MongoDB服务器的

打开端口有两种方法，一种是一次性生效，重启后需要重新打开端口。另外一种是永久生效，服务器重启也不影响端口的开放

在这我将MongoDB的端口设为27888

Linux重启后，端口需要重新开放：
```
iptables -I INPUT -p tcp --dport 27888 -j ACCEPT
```
永久生效：
在/etc/sysconfig/iptables文件内加上下面这句命令
```
-A INPUT -p tcp -m tcp --dport 27888 -j ACCEPT

# 重启iptables
service iptables restart
```
如果`iptables`文件不存在，那我们直接创建一个就好了

## 服务器重启后自动打开MongoDB

有时候我们会重启服务器，但是难免有些东西会忘记打开，或者别的同事重启了服务器，他不知道怎么开

所以我们需要配置成MongoDB自动打开，这样就能避免很多不必要的麻烦

```
cd /etc/rc.d

# 将MongoDB的启用方式追加到rc.local文件内
echo '/usr/local/mongoDB/bin/mongod --config /usr/local/mongoDB/conf/mongodb.conf' >> rc.local
```

如果我们对MongoDB设置了自定义端口，可不要忘了端口也要配置成开机自动打开哦

在这也写上Linux关机/重启的命令吧，可以验证下我们配置的重启生效的内容是否成功

## Linux关机/重启
```
重启命令：    reboot（现在重新启动计算机）

shutdown now（现在重新启动计算机）

关机命令：shutdown -h now（立刻进行关机）

    halt（立刻进行关机）

    poweroff（立刻进行关机）
```
一般情况下我们连接Linux服务器都是使用远程连接，所以执行`reboot`即可，关机命令就别尝试了，知道这玩意就好了，毕竟……远程连接的情况下，关了后你咋开机呢？？？

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pbxlmfqbj306008f0t1.jpg)

## 微信扫码关注公众号「闹闹吃鱼」，每周都有好分享