#  linux的find命令详解
find命令是用来在给定的目录下查找符合给定条件的文件

## 查找条件
**1、根据名称查找**

　-name "PATERN"
　-iname "PATERN"： 不区分名称字母大小写
  -regex "PATTERN"：基于正则表达式的模式查找，匹配的是整个路径，而非单个文件名。


**2、根据文件从属关系查找**

-user USERNAME：查找属主指定用户的所有文件；
-group GRPNAME：查找属组指定组的所有文件； 
-uid UID：查找属主指定的UID的所有文件；
-gid GID：查找属组指定的GID的所有文件；
-nouser：查找没有属主的文件；
-nogroup：查找没有属组的文件；

**3、根据文件的类型查找**

-type：根据不同的文件类型筛选

|type|描述|
|--|--|
|f	|普通文件|
|d      |目录文件|
|l	|符号链接文件|
|b	|块设备 文件|
|c	|字符设备文件|
|p	|管道文件|
|s	|套接字文件|
 
**4、根据文件的大小查找**

-size [+|-]#UNIT (+表示大于 -表示小于)
常用单位：c(字节),k, M, G
　　　　　　  
**5、根据时间戳查找**
按照atime(文件的最后访问时间)、mtime(文件的最后修改时间)、ctime(文件最后改变时间)、amin、mmin、cmin


```
 #find  /tmp  –atime  +5           //表示查找在五天内没有访问过的文件
 #find  /tmp  -atime  -5            //表示查找在五天内访问过的文件
```
**6、根据权限查找**
-perm 权限  不常用
    
**7、-exec 执行其他操作**



```
find .-type f -user root -exec chown tom {} \;
#删除
find $HOME/. -name "*.txt" -ok rm {} \;
find . -type f -name "*.log" -print0 | xargs -0 rm -f
find . -type f -name "*.txt" -exec cat {} \;> all.txt
find . -type f -mtime +30 -name "*.log" -exec cp {} old \;
find . -type f -name "*.txt" -exec printf "File: %s\n" {} \;
#统计
find . -type f -name "*.php" -print0 | xargs -0 wc -l
#压缩
find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz
#移动
find /home/ -type f | xargs -n 1 -t -I {} mv {} /tmp/
#介绍
xargs -n1 多行输入单行输出：
xargs的一个选项-I，使用-I指定一个替换字符串{}，这个字符串在xargs扩展时会被替换掉，当-I与xargs结合使用，每一个参数命令都会被执行一次：

```



