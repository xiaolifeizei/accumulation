### 解决端口占用

1、查看端口PID

> lsof -i:5000

2、关闭进程

> kill -9 pid

### 设置文件权限

> sudo chomd 777 filename

### 解决"/bin/bash^M: bad interpreter: No such file or directory"

```
vim filename

:set ff=unix

:wq
```
