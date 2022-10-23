# dirsearch工具扫描参数

## 一、扫描的目标

```shell
-u,--url                    目标url
-l,--url-list=FILE          目标url文件路径 
--stdin                     从标准输入中指定url
--cidr                      目标网段
--raw=File                  从文件中读取request报文,通过-schema指定策略(如--schema https)

```

## 二、扫描的字典类型

```shell
-e,--extensions             包含的文件拓展名(逗号分隔)  如-e php,asp  
-X,--exclude-extensions     排除的文件拓展名(逗号分隔)  如-X asp,jsp
-f,--force-extensions       在字典的每条记录后面添加文件拓展名

dirsearch默认只会替换字典中%EXT%为指定的extensions 
如-e php  Wishlist.%EXT%-->Wishlist.php
```


## 三、字典格式设置

```shell
-w,--wordlists              自定义wordlist(以逗号分隔)
--prefixes                  添加自定义前缀
--suffixes			        添加自定义后缀
--only-selected      	    筛选出指定的文件拓展名或无文件拓展名的目录
--remove-extensions   	    移除所有wordlist的后缀名 (admin.php --> admin)
-U, --uppercase      	    将字典转换为大写
-L, --lowercase		 	    将字典转换为小写
-C, --capital        	    第一个字母大写剩下字母小写
```


## 四、响应结果的过滤

```shell
-i                    		保留的响应状态码(以逗号分隔,支持指定范围) 如(-i 200,300-399)
-x                    		排除的响应状态码(以逗号分隔,支持指定范围)  如(-x 301,500-599)
--exclude-sizes             通过大小排除(以逗号分隔) 如(123B,4KB)
--exclude-texts     		通过文本内容排除响应('Not found', 'Error')
--exclude-regexps  			通过正则匹配排除响应('Not foun[a-z]{1}', '^Error$')
--exclude-redirects 		通过正则跳转目标排除响应('https://okta.com/*')
--minimal 					最小响应报文长度
--maximal 					最大响应报文长度
```


## 五、请求相关设置

```shell
-m,--http-method    	    HTTP请求方法 默认为GET
-d,--data   				HTTP请求数据
-H,--header  				请求头 如(-H 'Referer: example.com' -H 'Accept: */*')
--header-list=FILE  		从文件中读取请求头
-F,--follow-redirects  	    跟随HTTP跳转
--user-agent  			    设置user-agent字段
--cookie  				 	设置cookie
```


## 六、连接相关设置

```shell
--timeout=TIMEOUT     		连接超时时间
--ip=IP   					服务器ip地址
-s DELAY, --delay=DELAY     每次请求间隔的时间
--proxy=PROXY   			代理url 支持HTTP和SOCKS代理 如(localhost:8080, socks5://localhost:8088)
--proxy-list=FILE 		    包含代理服务器的地址
--matches-proxy=PROXY       Proxy to replay with found paths
--scheme   					默认的策略 用于从文件中导入请求或url中不包含协议
--max-retries 			    最大重连次数
-b,--request-by-hostname 	强制通过域名连接，(默认为了速度,使用ip连接)
--exit-on-error    		    出现错误时退出
--debug					    Debug模式
```


## 七、通用设置

```shell
--version  显示dirsearch的版本
-h --help   帮助提示
-r,--recursive        	    递归爆破
-R,--recursion-depth  		最大递归的层数
-t,--threads         		线程数
--subdirs             		扫描子目录 如(admin/ 则www.example.com/admin/+字典)
--exclude-subdirs     		在递归扫描中排除的子目录
-q,--quiet-mode             安静模式
--full-url                  打印出完整的url
--no-color                  无颜色输出信息
```

## 八、输出模式

```shell
--simple-report=OUTPUTFILE
--plain-text-report=OUTPUTFILE
--json-report=OUTPUTFILE
--xml-report=OUTPUTFILE
--markdown-report=OUTPUTFILE
--csv-report=OUTPUTFILE
```

可以通过修改default.conf文件去修改dirsearch的默认配置(默认文件拓展名，超时时间，wordlist所在目录等)

## 九、常见的用法

采用默认设置扫描目标url
python3 dirsearch.py -u https://target

使用文件拓展名为php,html,js的字典扫描目标url
python3 dirsearch.py -e php,html,js -u https://target

采用指定路径的wordlist且拓展名为php,html,js的字典扫描目标url
python3 dirsearch.py -e php,html,js -u https://target -w /path/to/wordlist

递归扫描
python3 dirsearch.py -e php,html,js -u https://target -r

设置递归层数为3
python3 dirsearch.py -e php,html,js -u https://target -r -R 3

指定线程(不建议线程数调整过大，可能会影响扫描的结果)
python3 dirsearch.py -e bak,zip,tgz,txt -u https://target -t 30

使用前缀后缀
python3 dirsearch.py -e php -u https://target --prefixes .,admin,_,~(前缀)

tools
变为
.tools
admintools
_tools
~tools

python3 dirsearch.py -e php -u https://target --suffixes ~,/(后缀)

index.php
internal
变为
index.php~
index.php/
internal~
internal/

对文件拓展名的限制
python3 dirsearch.py -e asp,aspx,htm,js -u https://target -X php,jsp,jspx

admin
admin.%EXT%
index.html
home.php
test.jsp
变为
admin
admin.asp
admin.aspx
admin.htm
admin.js
index.html

从文件中导入http请求的用法
请求格式

GET /admin HTTP/1.1
Host: admin.example.com
Cache-Control: max-age=0
Accept: */*

通过–schema指定策略 默认为http
子目录
python3 dirsearch.py -e php,html,js -u https://target --subdirs admin/,folder/,/

使用代理
python3 dirsearch.py -e php,html,js -u https://target --proxy 127.0.0.1:8080

使用代理集合
python3 dirsearch.py -e php,html,js -u https://target --proxylist proxyservers.txt

POST请求
python3 dirsearch.py -e php,txt,zip -u https://target -w db/dicc.txt -t 100 -m POST --data "username=admin"

TIPS
控制每秒请求的速率:-t <rate> -s 1
寻找备份文件和配置文件:--suffixes ~与--prefixes .
取消强制文件拓展名:添加%NOFORCE%
只想找到寻找文件夹和目录:--no-extension+--suffixes /
对于网段爆破时采用:--cidr+-F+-q(跟随跳转+安静模式)减少无效结果