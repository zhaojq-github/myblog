[TOC]



# 解决git pull/push每次都需要输入密码问题

## 问题

如果我们git clone的下载代码的时候是连接的[https://而不是git@git](https://%E8%80%8C%E4%B8%8D%E6%98%AFgit@git/) (ssh)的形式，当我们操作git pull/push到远程的时候，总是提示我们输入账号和密码才能操作成功，频繁的输入账号和密码会很麻烦。

## 解决办法：

git bash进入你的项目目录，输入：

```
git config --global credential.helper store
```

然后你会在你本地生成一个文本，上边记录你的账号和密码。当然这些你可以不用关心。
然后你使用上述的命令配置好之后，再操作一次git pull，然后它会提示你输入账号密码，这一次之后就不需要再次输入密码了。

以上转载自：[解决git pull/push每次都需要输入密码问题](http://m.blog.csdn.net/nongweiyilady/article/details/77772602)

无论是用编辑器pull或者push还是在git bash里pull或push，每次都需要输入密码，上述代码经测试，在一个工程里用过，在其他工程内同样生效。无论你是哪种方式pull或push代码都不用输入账号密码了。





https://github.com/yonyouyc/blog/issues/6