# Navicat for oracle创建数据库



## 创建数据库

使用Oracle默认账户“system”或者自己的账户连接Oracle

![img](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/2f70026391b2ec12b8d93a6ef8358416.jpg)

新建一个表空间

![img](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/4c98da6a80e27a126bb668c7e38f839f.jpg)

开启自动扩展

![image-20230206093412157](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230206093412157.png)

 创建一个新用户

![img](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/41c0756fefd533eebbd7d4e44f2ad841.jpg)

设置信息如下 ，默认表空间设置为刚刚新建的表空间

**！！注意用户名需要设置为全大写英文字母！！**否则在后面连接用户时会出现“用户名或者密码无效”的错误，猜想应该时Oracle在创建用户名是没有要求，但在连接用户是却对用户名进行了检查，所以造成无法连接， 

![image-20230206093755724](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230206093755724.png)

为该用户设置“成员属于”

![image-20230206093852408](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230206093852408.png)

设置“服务器权限”

![image-20230206093925650](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230206093925650.png)

至此，数据库已经创建完毕了

## 连接验证

![img](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/c38b7dd3c90ff6dccc52eb10396ccf89.jpg)

注意登录角色的选择

![image-20230206094254609](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230206094254609.png)