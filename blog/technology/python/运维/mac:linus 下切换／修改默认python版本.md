# mac/linus 下切换／修改默认python版本

> 我的需求是将mac／linus下的默认python2.7 切换成默认的python3

## 查看本级上的python3命令目录

```
which python3
# 我的是 ／usr/local/bin/python3.612
```

## 修改bashrc配置文件

```
vi ~/.bashrc
```

想要为某个特定用户修改 Python 版本，只需要在其 home 目录下创建一个 alias(别名) 即可。打开该用户的 ~/.bashrc 文件，添加新的别名信息来修改默认使用的 Python 版本。

```
alias python='/usr/local/bin/python3.6'
```

## 重新加载 .bashrc 文件，使操作生效

```
. ~/.bashrc
```

## 验证

```
python 
```





https://blog.csdn.net/adley_app/article/details/79245736