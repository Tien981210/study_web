# shell

## ls--查看文件和目录

```
ls
```

## export--定义变量

```
# 定义变量
export ZSH=/Users/xingkongbj/.oh-my-zsh

# 修改环境变量
export PATH=$HOME/bin:/usr/local/bin:$PATH
```

## source--引入文件

```
source $ZSH/oh-my-zsh.sh
```

## alias--语句别名

```
alias runios="react-native run-ios"
```

## &--前后语句同时执行

```
node node1.js & node node2.js
```

## &&--前语句成功后，执行后语句

```
node node1.js && node node2.js
```

## ||--前语句失败后，执行后语句

```
node node1.js || node node2.js
```

## |--管道参数，前语句执行结果传给后语句

```
node --v8-options | grep 'in progress'
```

## grep--结果过滤

```
node --v8-options | grep 'in progress'
```

## echo--输出内容

```
echo hello world
```

## pwd--输出当前路径

```
pwd
```

## exit--退出

```
# 0为成功
exit 1
```

## whereis--查看安装目录

```
whereis nginx
```

## whoami--查看当前用户

```
whoami
```

## netstat--查看网络连接

```
netstat -an|grep 80
```

## lsof--查看端口服务

```
lsof -i :9999
```

## ps--查看进程

```
ps -ef|grep nginx
```

## kill--关闭进程

```
# 正常退出
kill -QUIT 进程号
kill -3 进程号

# 快速停止
kill -INT 进程号
kill -2 进程号
kill -TERM 进程号
kill -15 进程号

# 强制退出
kill -KILL nginx
kill -9 nginx
```

## useradd--添加用户

```
useradd -g www www
```

## groupadd--添加用户组

```
groupadd www
```

## >/dev/null--输出到黑洞

```
>/dev/null # 默认是1，标准输出，即屏幕显示
2>/dev/null # 错误输出到黑洞
2>&1 # 把错误输出2重定向到标准输出1
```