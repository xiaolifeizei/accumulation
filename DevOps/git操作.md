### 一、关联到远程仓库

1、创建新的仓库

```bash
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:xiaolifeizei/gittest.git
git push origin master
```

2、使用一个已经存在的仓库

```bash
git remote add origin git@github.com:xiaolifeizei/gittest.git
git push origin master
```

### 二、从远程仓库创建分支

```bash
git checkout -b dev origin/master
```

### 三、查看本地分支

```bash
git branch
```

当前分支前面有一个*****号

### 四、将远程仓库的代码更新到本地分支

```bash
git pull origin dev
```

### 五、git 同时推送多个仓库

```bash
git remote add origin 远程地址1
git remote set-url --add origin 远程地址2
# 推送到多个仓库
git push -u origin master
```

### 六、git生成ssh

1、打开本地git bash,使用如下命令生成ssh公钥和私钥对

```bash
ssh-keygen -t rsa -C 'xxx@xxx.com' 
```

然后一路回车(-C 参数是你的邮箱地址)

2、然后会出现：Enter file in which to save the key (/Users/当前登录用户名/.ssh/id_rsa):

```bash
回车
```

3、如果你的.ssh/id_rsa已经，则会出现：

```bash
/Users/当前登录用户名/.ssh/id_rsa already exists.
Overwrite (y/n)? y
```

```bash
输入：y  （重新覆盖）
输入：n  （不覆盖）
```

4、设置你的密码（位数不要太短，尽量设置6位）

5、现在只需要查看本机ssh公钥，获取得到它

使用文本编辑器打开

```bash
C:\Users\当前登录用户名\.ssh\id_rsa.pub
```

6、文件中的内容就是我们需要的ssh key

![image-20230411103641802](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230411103641802.png)

7、解决 git SSL certificate problem: self signed certificate

```bash
git config --global http.sslVerify false
```

8、解决：git:remote:HTTP Basic:Access denied

清空本地保存的用户名和密码：

```bash
git config --system --unset credential.helper
```

要以**管理员身份**运行，不然会出现：

```bash
error: could not lock config file C:/Program Files/Git/mingw64/etc/gitconfig: Permission denied
```

全局生效：

```bash
git config --global credential.helper store
```

或者打开gitconfig文件，写入：

```ini
##如果不想保存，则删除即可
[credential]
    helper = store
```

