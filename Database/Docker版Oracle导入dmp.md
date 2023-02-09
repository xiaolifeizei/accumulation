## Docker版Oracle导入dmp

1. 打开终端

2. 复制文件到oracle容器内

   ```bash
   docker cp xxx.dmp oracle11g:/opt/oracle/dpdump/
   ```

3. 进入docker 容器

   ```bash
   docker exec -it oracle11g /bin/bash
   ```

4. 使用oracle用户

   ```bash
   su -oracle
   ```

5. 输入imp命令

   ```bash
   imp
   ```

6. 输入用户名和密码

7. 提示是否只导入数据时按回车

   ```bash
   Import data only(yes/no):no
   ```

8. 导入数据文件

   ```bash
   Import file:/opt/oracle/dpdump/xxx.dmp
   ```

9. 一路回车