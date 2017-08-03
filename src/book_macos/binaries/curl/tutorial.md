---
index: true
type: api
---

## curl 查看网站源码
```
curl "https://www.baidu.com"
```

## curl 保存网站页面
```
curl "https://www.baidu.com" > /tmp/baidu.html
```
你会看到一条进度条，然后源码就被重定向到了/tmp/baidu.html。
可以在浏览器中输入file:///tmp/baidu.html看到一张完整的百度首页。
### -o/-O 参数
 1. -o：将文件保存为命令行中指定的文件名的文件中
 1. -O：使用URL中默认的文件名保存文件到本地
当然下载页面用-o参数看上去更加高级一点。

```
curl -o /tmp/baidu2.html "http://www.baidu.com"
```

如果URL中具有文件名，那么使用 -O 参数也会更方便些，它将直接用url中的文件名称来保存页面。

```
1 # 将文件下载到本地并命名为mygettext.html
2 curl -o mygettext.html http://www.gnu.org/software/gettext/manual/gettext.html
3 
4 # 将文件保存到本地并命名为gettext.html
5 curl -O http://www.gnu.org/software/gettext/manual/gettext.html
6 # 同样可以使用转向字符">"对输出进行转向输出
```

### -s 参数
curl 安静点
-s参数可以屏蔽进度条之类的输出，稍微清净点
```
curl -s -o /tmp/baidu3.html "http://www.baidu.com"
```

## curl 下载单个文件
> 默认将输出打印到标准输出中(STDOUT)中

## curl 同时下载多个文件

```
curl -O URL1 -O URL2
```
> 若同时从同一站点下载多个文件时，curl会尝试重用链接(connection)。

## curl 重定向
通过-L选项进行重定向
默认情况下CURL不会发送HTTP Location headers(重定向).当一个被请求页面移动到另一个站点时，会发送一个HTTP Loaction header作为请求，然后将请求重定向到新的地址上。
例如：访问google.com时，会自动将地址重定向到google.com.hk上。

```
1 # 让curl使用地址重定向，此时会查询google.com.hk站点
2 curl -L http://www.google.com
```

## curl 断点续传
通过使用-C选项可对大文件使用断点续传功能，如：
```
# 当文件在下载完成之前结束该进程
$ curl -O http://www.gnu.org/software/gettext/manual/gettext.html
##############             20.1%

# 通过添加-C选项继续对该文件进行下载，已经下载过的文件不会被重新下载
curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html
###############            21.1%
```

## curl 使用网络限速
通过--limit-rate选项对CURL的最大网络使用进行限制
```
1 # 下载速度最大不会超过1000B/second
2 
3 curl --limit-rate 1000B -O http://www.gnu.org/software/gettext/manual/gettext.html
```

## curl 下载指定时间内修改过的文件
当下载一个文件时，可对该文件的最后修改日期进行判断，如果该文件在指定日期内修改过，就进行下载，否则不下载。
该功能可通过使用-z选项来实现

```
1 # 若yy.html文件在2011/12/21之后有过更新才会进行下载
2 curl -z 21-Dec-11 http://www.example.com/yy.html
```

## curl 授权
在访问需要授权的页面时，可通过-u选项提供用户名和密码进行授权

```
1 curl -u username:password URL
2 
3 # 通常的做法是在命令行只输入用户名，之后会提示输入密码，这样可以保证在查看历史记录时不会将密码泄露
4 curl -u username URL
```

## curl 从FTP服务器下载文件
CURL同样支持FTP下载，若在url中指定的是某个文件路径而非具体的某个要下载的文件名，CURL则会列出该目录下的所有文件名而并非下载该目录下的所有文件

```
1 # 列出public_html下的所有文件夹和文件
2 curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/
3 
4 # 下载xss.php文件
5 curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/xss.php
```

## curl 上传文件到FTP服务器
通过 -T 选项可将指定的本地文件上传到FTP服务器上

```
# 将myfile.txt文件上传到服务器
curl -u ftpuser:ftppass -T myfile.txt ftp://ftp.testserver.com

# 同时上传多个文件
curl -u ftpuser:ftppass -T "{file1,file2}" ftp://ftp.testserver.com

# 从标准输入获取内容保存到服务器指定的文件中
curl -u ftpuser:ftppass -T - ftp://ftp.testserver.com/myfile_1.txt
```

## curl 设置代理
-x 选项可以为CURL添加代理功能

```
1 # 指定代理主机和端口
2 curl -x proxysever.test.com:3128 http://google.co.in
```

[参照 https://curl.haxx.se/docs/httpscripting.html](https://curl.haxx.se/docs/httpscripting.html)