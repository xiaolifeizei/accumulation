## 统计Nginx接口的访问量

### 拿到access.log文件

1. 准备好access.log文件

   ![image-20230208111204149](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111204149.png)

2. 上传到测试服务器或虚拟机中（Centos系统）

   ![image-20230208111226121](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111226121.png)

### 分析文件内容

1. cd到access.log的目录

   ![image-20230208111249516](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111249516.png)

2. 执行命令

   ```bash
   awk '{print $7}' access.log|sort | uniq -c |sort -n -k 1 -r > test.txt
   ```

   ![image-20230208111306027](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111306027.png)

3. 得到分析文件

   ![image-20230208111323305](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111323305.png)

### 导出到本机

1. 将分析结果导到本机中

   ![image-20230208111340630](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111340630.png)

2. 打开查看结果

   ![image-20230208111358973](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111358973.png)

### 导入到表格（以WPS为例）

1. 打开wps表格

   ![image-20230208111422058](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111422058.png)

2. 点击数据->导入数据

   ![image-20230208111442802](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111442802.png)

3. 选择导入数据

   ![image-20230208111500604](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111500604.png)

4. 弹出的对话框中选择确定

   ![image-20230208111516993](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111516993.png)

5. 选择直接打开数据文件->选择数据源->选择刚刚导出来的分析结果

   ![image-20230208111535394](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111535394.png)

6. 默认即可，点下一步

   ![image-20230208111556413](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111556413.png)

7. 默认即可，下一步

   ![image-20230208111617448](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111617448.png)

8. 分割符号选择空格，点击下一步

   ![image-20230208111632826](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111632826.png)

9. 点击完成

   ![image-20230208111910319](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111910319.png)

10. 成功导入

![image-20230208111712382](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111712382.png)

### 求和

![image-20230208111735220](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111735220.png)

### 计算访问比例

1. 计算公式

   ![image-20230208111754231](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111754231.png)

2. 拉动计算全部

![image-20230208111829495](https://raw.githubusercontent.com/xiaolifeizei/myImages/master/picgo/image-20230208111829495.png)
