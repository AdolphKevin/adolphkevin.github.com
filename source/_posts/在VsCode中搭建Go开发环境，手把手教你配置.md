---
title: 在VsCode中搭建Go开发环境，手把手教你配置
tags: VsCode
categories: golang
abbrlink: 19941
date: 2019-05-18 00:00:00
---
![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd20bhroj30h90d9ag1.jpg)

前几天我聊了聊我自己从一个什么都不懂的小白，到现在只懂一点的学习历程

所谓万事开头难，学习Go语言最难的时候，也就是咱们啥都不知道的时候

之前我说了我学Go语言入门花了好几个晚上，这个时间不是花费在学习Go语言语法上，而是花在了搭建环境上…**这篇文，我把我当初所有踩的坑都写到了**

我自己的开发环境是Windows 10 +VS Code ，都知道VS Code是一个可以让开发者自己高度定制化的编译器，而也正是因为这一点，从0开始搭建一个开发环境简直是累的一匹

之前有好些个童鞋来私聊我，问我编译器选择和配置的事情，我都是推荐使用VsCode，那**今天就说说怎么把VSCode配置成Golang的编译器吧**

因为我手上只有Windows的电脑，所以就只能说下Windows环境如何配置了

## Golang的安装
先去官网上下载Golang的安装包：[点击访问下载地址](https://golang.org/dl/)

Windows的安装就很简单了，打开安装包后，一直Next就好了。安装时记好自己的安装路径，我自己就默认安装到C盘了

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd2a730nj30ks03cq2z.jpg)

安装好Go安装包后，需要配置环境变量，印象中记得Go在1.6版本后，这些环境变量都不需要我们手动配置了，安装的时候都会自动配置好

不过为了避免你们有些人进行了一些蛇皮操作，导致安装包没有自动配置环境变量，我这还是简单唠嗑一下环境变量的配置吧（已经配置好了就不用看了，直接看下一部分吧）

## Go环境变量配置
所谓千言万语不如一张图，给你们截了图，也在图上做了标注，按着图片上的步骤来就好了，简单又明了

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd2lrnbpj311u0igthq.jpg)

配置`GOPATH`的的时候，可以看到下图，我路径设为了`E`盘，是因为`GOPATH`是我们的工作区，说白了就是我们做项目时放代码的地方，所以自己随便设置就好了

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd2s5kqzj30gl09l74m.jpg)

如果要变更`GOPATH`的路径，建议把原来`GOPATH`里的`pkg`、`bin`、`src`三个文件夹也复制过去，不然到时候我们开发时又要重新配置依赖包，不知情的你，可能还认为出BUG了呢

`GOROOT`是Go的安装路径，`Path`变量里的选择安装路径里的`bin`文件路径就好了

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd2zeh5pj30oh0g20ut.jpg)

## 配置Go开发环境

我是提倡使用VSCode，所以也就只说VSCode了，如果你们热爱别的编译器，那我在这也只能告诉你如何安装Go的依赖包了…

打开我们的VSCode，安装`Go`插件，就这一个就可以了

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd377hdlj30k60a00uv.jpg)

好像没有梯子还不给下载，如果安装失败会提示你从网页上去下载到本地安装。**以防万一你们下载不了，我把这个插件下载好打包放在公众号里了，你们直接在我公众号后台回复「插件」就可以拿到下载地址**

## 安装Go的依赖包

兴致勃勃的打开了编译器，刚准备写个`Hello World`震惊世界的，结果第一行`package`都还没有写完，VSCode就给我这个提示……

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd3dcy9ej30d4085t93.jpg)

天真的你一而再再而三的点`Install All`，放心，不管用的，咱家的墙比天还高，搭梯子也翻不过去

不信？你看呗

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd3ljyhzj30f0084q39.jpg)

看到各种Failed，心里一万匹什么来着？不管了，反正就是万马奔腾吧

那咋办？就这样不管了？还没入门就放弃？

放心吧，有闹闹我在，怎么说今天也得让你用VSCode写个`Hello Word`爽爽

当`go get`不好使的时候，**咱们就先去Github上把需要的package下载到本地，建议用Git把包给`Clone`下来，以后包有更新了，直接`pull`就可以更新**，再把包给重新安装一次就好了

**此时VSCode提示的`Failed`就有用了**，比如提示了`Installing github.com/ramya-rao-a/go-outline FAILED`，我们直接把网址复制下来放到浏览器，就可以看到这个Github的仓库

拿到下载链接后直接`clone`到本机上

**下载好了放哪里呢？放`GOPATH`的`src`路径下面或者`GO ROOT`的`src`下都可以**

只是**文件夹命名要按照网址路径命名，这点一定要注意**，如果不明白，下面我有举例说明

像`golang.org/x/tools/cmd/guru`这种不是在Github上的怎么搞？其实跟放在Github上的包下载是一样的操作，直接把这段链接放到浏览器里就可以打开网站，网站里面会有下载地址的，如下图

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd3slqucj30or0bigmf.jpg)

**如果不知道自己的`GOPATH`或者`GOROOT`路径在哪里**，可以按`win+R`键，输入`cmd`，打开「命令提示符」输入`go env`，即可看到自己的`GOPATH`路径以及`GOROOT`路径。也可以像文章开篇那样，跑去环境变量配置的地方找

好了，拿个实际案例跟你们说说，以刚刚`github.com/ramya-rao-a/go-outline`这个包为例

打开链接`github.com/ramya-rao-a/go-outline`

将这个包`clone`到本地的`GO PATH`路径下（我是放在`GO PATH`路径下的，你们若是不想`GO PATH`内存在与自己项目无关的文件，可以放在`GO ROOT`路径下）

```
git clone https://github.com/ramya-rao-a/go-outline.git
```

这个`package`的路径最后是这样的，如下图：

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd43111vj30l604qq38.jpg)

**悄悄地告诉你**，其实只要在`src`目录下打开`bash`执行`clone`就ok了~哈哈哈哈哈

依赖包都下载好后，就可以进行安装了。可以在`CMD`中，或者直接在VSCode里按``Ctrl + ` ``打开`TERMINAL`进行安装。

输入`go install`命令，执行`pkg`的安装

```
go install golang.org/x/tools/go/gcexportdata
go install github.com/mdempsky/gocode
go install golang.org/x/tools/cmd/guru
go install golang.org/x/tools/cmd/gorename
...
```

说到这，我想也差不多了，**剩下的我相信你们自己也能搞定**，毕竟我想让你们知道该怎么弄，这样以后你们要使用其它的第三方依赖包时，也知道该如何下载安装

**如果实在还没弄明白，可以关注我公众号「闹闹吃鱼」，有什么不明白的我可以为你解答**

## Hello Word

好了，编译器已经配置好了，接下来就是我们的`Hello Word`了

因为`GOPATH`是我们的工作区，所以我们自己搭建的项目，也要放在`GO PATH`的`src`路径下，文件路径如下图所示：

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd4eeig1j30k603yt8u.jpg)

一切就绪，就差Code~

```Go
package main

import "fmt"

func main() {
	fmt.Printf("Hello word")
}
```

嗯哼？到这步了，该试试怎么运行了，那就直接运行好了~
```
$ go run hello.go
```

可以看到咱们想要的结果出来了~~~

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd5rpbyoj30dq01wq2v.jpg)

## VS Code配置
用了编译器，还要用命令行来运行程序，那要VS Code干嘛？

我们熟悉的Debug，按了F5没效啊！还是提示我有问题~~~

那我们就来调整下Debug的配置好了，如下图所示：

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd5lu7lzj30l509st9d.jpg)

launch.json 文件内的内容，将下面的配置全部复制上去就好了~

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "LaunchGo",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "remotePath": "",
            "port": 5546,
            "host": "127.0.0.1",
            "program": "${fileDirname}",
            "env": {
                "GOPATH": "E:/GoCode",
                "GOROOT": "C:/Program Files/Go"
            },
            "args": [],
            //"showLog": true
        }
    ]
}
```

配置好后，我们在`hello.go`文件内，直接按F5就能进入调试状态啦~

为了让你们在编写Go项目时有跟我一样的良好体验，我把我Vs Code的设置也拿给你们

下面这张图是如何打开`setting.json`文件

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pd4zinekj30hd0f73zi.jpg)

打开之后把下面的内容全部Copy进去就好了，其中的`go.goroot`与`go.gopath`，你需要改成你自己的Go环境路径
```
{
    "editor.wordWrap": "on",
    "editor.minimap.renderCharacters": false,
    "editor.minimap.enabled": false,
    "terminal.external.osxExec": "iTerm.app",
    //"go.useLanguageServer": true,
    "go.docsTool": "gogetdoc",
    "go.testFlags": ["-v","-count=1"],
    "go.buildTags": "",
    "go.buildFlags": [],
    "go.lintFlags": [],
    "go.vetFlags": [],
    "go.coverOnSave": false,
    "go.useCodeSnippetsOnFunctionSuggest": false,
    "go.formatTool": "goreturns",
    "go.gocodeAutoBuild": false,
    "go.goroot": "C:\\Program Files\\Go",
    "go.gopath": "E:\\GoCode",
    "go.autocompleteUnimportedPackages": true,
    "go.formatOnSave": true,
    "window.zoomLevel": 0,
    "debug.console.fontSize": 16,
    "debug.console.lineHeight": 30,
    "[javascript]": {
        "editor.defaultFormatter": "HookyQR.beautify"
    },
    "[html]": {
        "editor.defaultFormatter": "HookyQR.beautify"
    },
}
```

现在我们的开发环境都已经配置好了，从现在开始，我们就可以happy的编写我们的Go项目啦~~

## Go语言学习资料分享

到今天，我手上Go语言的学习资料终于整理完了，**有从入门到实战的Go语言教学视频（嫖的某培训班的视频）**

**也有从入门到实战再到精通的一条龙技术书籍**

我是属于比较喜欢看书的，对我来说看书的学习效率比看视频要好，但是有的人基础差了点，还是需要看视频，让人手把手带入门才行。所以帮你们嫖了一个全面并且详细的中文视频教程

你们都知道的，我给你们分享资源，从来就没有什么套路，退出文章后，在我**公众号后台回复「Go资料」**就可以领取下载链接了

再吐槽一下，百度网盘这货我是彻底抛弃了，因为就算我开了VIP，你们的下载速度还是慢的一匹，并将经常连接中断。

想想80kb/s的速度就美滋滋，这速度下载10GB的视频资源，明年应该能下载好吧？

现在换了别的网盘，下载速度的体验还算是可以的，就是有时效性30天，**要是某天下载链接要是失效了，你们私聊我，我就去维护一下。**

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pbxlmfqbj306008f0t1.jpg)

## 关注公众号「闹闹吃鱼」领取更多资源，不仅仅只是技术