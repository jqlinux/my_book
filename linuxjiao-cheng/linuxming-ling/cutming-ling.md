cut命令用来显示行中的指定部分，删除文件中指定字段。cut经常用来显示文件的内容，类似于下的type命令。

说明：该命令有两项功能，其一是用来显示文件的内容，它依次读取由参数file所指 明的文件，将它们的内容输出到标准输出上；其二是连接两个或多个文件，如cut fl f2 &gt;f3将把文件fl和几的内容合并起来，然后通过输出重定向符“&gt;”的作用，将它们放入文件f3中。

当文件较大时，文本在屏幕上迅速闪过（滚屏），用户往往看不清所显示的内容。因此，一般用more等命令分屏显示。为了控制滚屏，可以按Ctrl+S键，停止滚屏；按Ctrl+Q键可以恢复滚屏。按Ctrl+C（中断）键可以终止该命令的执行，并且返回Shell提示符状态。

**命令参数**

```
-b：仅显示行中指定直接范围的内容； 
-c：仅显示行中指定范围的字符； 
-d：指定字段的分隔符，默认的字段分隔符为“TAB”； 
-f：显示指定字段的内容； 
-n：与“-b”选项连用，不分割多字节字符； 
--complement：补足被选择的字节、字符或字段； 
--out-delimiter=<字段分隔符>：指定输出内容是的字段分割符； 
--help：显示指令的帮助信息； 
--version：显示指令的版本信息。
```

实例

```
cut -d: -f 1 /etc/passwd

root
bin
bin
daemon
adm
lp
sync
sync
shutdown
halt

cut -d: -f 1,2 /etc/passwd
root:x
bin:x
bin:x
daemon:x
adm:x
lp:x
sync:x

#--complement 选项提取指定字段之外的列（打印除了第二列之外的列）

cut -d: -f 1,2  --complement /etc/passwd
0:0:root:/root:/bin/bash
1:1:bin:/bin:/sbin/nologin
1:1:bin:/bin:/sbin/nologin
2:2:daemon:/sbin:/sbin/nologin
3:4:adm:/var/adm:/sbin/nologin
```

m

