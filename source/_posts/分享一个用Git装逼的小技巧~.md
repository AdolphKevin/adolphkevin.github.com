---
title: 分享一个用Git装逼的小技巧~
tags: Git
categories: Git教程
---
![](https://upload-images.jianshu.io/upload_images/15072499-0e0cecc41d7e602b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上次写完 [实用且简单的Git教程，轻松搞定多人开发](https://mp.weixin.qq.com/s?__biz=MzU2NzczMzk5Nw==&mid=2247483753&idx=1&sn=a8d654a7f61833b976f65a9e93b4f56c&chksm=fc99faebcbee73fd8ef1b8bd777f064d90f5a8c5a628da6814065028264b174e09b1b3551176&scene=21#wechat_redirect](https://mp.weixin.qq.com/s?__biz=MzU2NzczMzk5Nw==&mid=2247483753&idx=1&sn=a8d654a7f61833b976f65a9e93b4f56c&chksm=fc99faebcbee73fd8ef1b8bd777f064d90f5a8c5a628da6814065028264b174e09b1b3551176&scene=21#wechat_redirect))，得到的反馈信息简直超乎我的预期。

我前两天登掘金、简书、CSDN上把微信公众号的文章copy过去的时候，那阅读量和点赞数简直亮瞎我了……

按理来说，写的好，作为我公众号的读者们，你们都是我从别的平台辛辛苦苦一点一点的挖来的，对我更认可才对，居然没几个人给我点「好看」，也没见你们谁分享转发

我公众号所有文章的点赞数和阅读数全加起来，还不copy过去的文章的十分之一，让我很是伤心啊，点个「好看」表达一下对我文字的认可，我才更有动力写更好的文字与你们分享啊

作为一个主营微信公众号的号主，要靠别的平台的点赞数据，来获得成就感，我想我也是混的够惨了。

不过值得欣慰的是，最近有人私信我，跟我说“就是因为看了我那篇文章，才把GIT的命令行给记住的”。听了后我简直高兴的不行

GIt是个好东西，用了Git后，其它的版本控制器我是觉得真的不好用，而且命令行操作更是好用的不得了

上篇Git的命令行使用，基本已经解决了95%的问题。（只是从开发人员使用的角度）

这次做一点补充，**再给你们分享个小技巧，巨好用，还能装逼！**
![](https://upload-images.jianshu.io/upload_images/15072499-8e3b68ae10645f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Git Log的进阶使用
Git查看日志，单独使用```git log```来操作，每一个commit信息太多，干扰信息又太多，而且各种分支的合并也看不到，难受的很。

今天给你们分享一个装逼的命令行，结合上一篇的文章[实用且简单的Git教程，5分钟搞定Git](https://www.jianshu.com/p/a3f0f55c88fb)，现在就完全可以摆脱第三方软件来使用Git了，复制粘贴即可使用

> git log --graph --pretty=format:'%Cred%h%Creset - %C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative 

给你们看看效果，是不是巨牛逼？

![](https://upload-images.jianshu.io/upload_images/15072499-251d2d176bff7d3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

命令行这么长还这么复杂，我们该怎么记住呢？莫慌，再给你分享一个好东西

那就是Git的一个别名操作```alias```，使用这个命令，可以极大的提高我们的命令行输入效率

## alias效率为王

我们经常使用的命令有```branch```，```checkout```，```commit```之类的，虽然简单，但是输入起来也是麻烦，作为一个程序员，开发程序就是为了提高效率的，能动一下手指解决问题，就绝不动两下，要把「懒」给发挥到极致才行

比如看下面的例子：

```git
git config --global alias.ck checkout

git config --global alias.br branch

git config --global alias.ck commit
````
这样配置好了之后，我们以后使用这些命令，像```git checkout```，直接输入```git ck```就能完事。是不是666？

```alias.xx```点后面的```xx```就代表了我们设置的别名，使用的时候，直接输入别名就好了

像上面那么长的```git log```，咱们完全就可以使用```alias```来提高效率，下面的命令行直接复制粘贴就能使用了~

若是你不做任何修改，使用的时候输入```git lg```即可
```git
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset - %C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```

配个图给你们看看

![](https://upload-images.jianshu.io/upload_images/15072499-1dc99c74735ed04f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行后，使用的时候直接打开控制台输入```git lg```即可，又装逼，又省事

顺便提示一下，Git的命令行操作时，复制是```ctrl+insert```，粘贴是```shift+insert```，查看日志时，按「回车键」可以加载更多日志信息，按```q```键是退出日志查看

在```merge```后的```commit```，偶尔会弹出一个Linux对话框让你写备注

此时需要用到Linux指令才能操作

按```i```进入对话框编辑模式，将```commit```的备注内容写好后
按```ESC```退出编辑模式
按```:```+```wq```退出并保存即可

## 为什么要用命令行？
你们应该都知道，**第三方界面化软件操作Git，本质上其实都是用的Git命令行，一些复杂的操作都是直接将GIT组合好后直接执行**，只是软件把他们封装了起来，没让我们看到命令行罢了

之前我也是用可视化的第三方软件来使用Git命令行的操作，后来发现总是有着莫名其妙的问题，并且错误提示看起来就是一头雾水，后来干脆就学着使用命令行操作

使用命令行之后才发现，执行的速度以及准确度，**比用第三方软件的效率要高的多**，并且一些莫名其妙的问题也再也没遇见过了

因为软件是把一系列的Git命令给封装起来，而我们自己使用的时候，Git提交的逻辑顺序我们是很清楚的，这样一步一步走下来，只要逻辑是对的，就不会出错，就算出错了，**命令行操作时，错在哪，该怎么修正，都提示的一清二楚**，这也省下了我们拿着界面化软件的报错去找百度的时间。

刚开始用命令行操作的时候，是有一些不大习惯，但是用熟练之后，你完全就不会想打开第三方软件了~

当然了，**技术只是一个工具，工具的目的就是为了提升效率**，如果觉得使用GUI界面化的软件你的效率更高，那就按照自己的高效率方式去做就好了。

像我，我就是在追求效率的同时，还想着要能装装逼~~

我做为一个依赖Windows生态的码畜，一切都是可视化操作，唯一能有点能像电影里极客样的样子，也就是用Git命令行的时候了……

毕竟每次用Git时，屏幕上的命令框里，突突突的跳出这些命令，感觉自己就像电影里的极客那样帅

## 写在最后
今天周末，也就是爬上来跟你们聊会儿，顺便分享两个小技巧。以后不出意外每周都会这样，当然了，我是指每周分享个小技巧什么的，心情不错的时候，就顺带逼逼叨一下

分享的小技巧什么的，分享的范围你们可以后台留言告诉我你们想要看什么方面的，我就多写写你们想要看的，反正趁着现在关注我的人不多，基本上每个人都能照顾到，现在不压榨我，还等什么时候呢？

![](https://upload-images.jianshu.io/upload_images/15072499-3437bf10840001c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 扫描关注微信公众号「闹闹吃鱼」，每天都有好分享
