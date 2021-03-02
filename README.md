# nginx-demo

nginx 的出现，轻松解决了C10K的的问题，单机性能可以达到5W左右的并发，和Apache一样，虽然都是属于Http Server，但是nginx能支持的协议会更加丰富，也支持SMTP，POP3和IMAP协议。

HttpServer，WebServer，Application Server。Tomcat是Application Server，一般用于为应用服务器(Appcalition Server) 用来存放运行系统程序的服务器，负责处理程序中的业务逻辑，如Tomcat，Weblogic，jboss（现在大多数应用服务器也包含了Web 服务器的功能）

Http Server，http 服务器 一般用来访问静态的资源，而应用服务器可以动态地生成资源内容，比如Java的Servlet。所以nginx是一台httpserver，用来访问静态的资源。

nginx也可以通过模块开发来提供应用功能，Tomcat也可以直接提供Http服务，通常用在内网和不需要流控等小型服务场景（小型的应用服务器，不追求性能的业务，用不到nginx）

因为http服务器泡在应用服务器中，例如tomcat是也是一台http服务器，springboot也内嵌的tomcat，因为http服务器跑在应用服务器的前面，所以也叫代理服务器。

一般来说Http服务器和应用服务器是一起使用的，比如运行多个tomcat，通过Nginx实现负载均衡，或者把静态资源放在nginx，动态资源放在tomcat，实现动静分离。

![1614398707411](./img/1614398707411.png)



C10K就是单机支持1万个并发连接的问题(Concurrency 10K)。



#### HTTP 协议基本知识

##### IP 和 端口

IP 是分配给网卡的地址，用来在网络中定位一台设备，能够在庞大的网络中找到一台设备的话，就意味着可以进行通信。

IP的格式是四个8位的二进制整数，分成四段，每段可以转换成0-255的十进制数字。

由于一台设备上会运行很多程序，所以要在设备上要找到某个应用程序，还需要端口号，所以必须给每个程序分配一个端口。端口的范围是 0-65535。

当然，我们已知道的应用，例如FTP服务的21端口，HTTP的80端口，MySql的3306，Redis的6379端口。

##### HTTP 协议的请求类型

* GET

  * 请求指定的页面信息，并返回实体主题

* HEAD

  * 类似于GET请求，只不过在返回的响应中没有具体的内容

* POST

  * 向指定资源提交数据进行处理请求，数据被包含在请求体中。

* PUT

  * 从客户端向服务端传送的数据取代指定的文档内容。

* DELETE

  * 请求服务器删除指定页面

* CONNECT

  * HTTP/1.1 协议中预留给能够将连接改为管道放置的代理服务器

* OPTIONS

  * 允许客户端查看的服务器地性能。

* TRACE

  * 回显服务器收到的请求，主要用于测试和诊断。

  

##### HTTP 格式响应状态码有哪些

| 响应码 | 类型                           | 含义                       |
| ------ | ------------------------------ | -------------------------- |
| 1XX    | Informational(信息性状态码)    | 接受的请求正在处理         |
| 2XX    | Success(成功状态码)            | 请求正常处理完毕           |
| 3XX    | Redirection(重定向状态码)      | 需要进行附加操作以完成请求 |
| 4XX    | Client Error(客户端错误状态码) | 服务器无法处理请求         |
| 5XX    | Server Error(服务器错误状态码) | 服务器处理请求出错         |

请求、响应的Header字段有哪些?

https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html

##### 通用Header字段

| 字段              | 含义                                       |
| ----------------- | ------------------------------------------ |
| Cache-Control     | 控制缓存的行为                             |
| Connection        | 控制不再转发给代理的首部字母、管理持久连接 |
| Date              | 创建报文的日期时间                         |
| Pragma            | 报文指定                                   |
| Trailer           | 报文末端的首部一览                         |
| Transfer-Encoding | 指定报文主题的传输编码方式                 |
| Upgrade           | 升级为其它协议                             |
| Via               | 代理服务器的相关信息                       |
| Warning           | 错误通知                                   |

##### 请求Header字段

| 字段                | 含义                                        |
| ------------------- | ------------------------------------------- |
| Accept              | 用户代理可处理得媒体类型                    |
| Accept-Charset      | 优先的字符集                                |
| Accept-Encoding     | 优先的内容编码                              |
| Accept-Language     | 优先的语言(自然语言)                        |
| Authorization       | Web 认证信息                                |
| Expect              | 期待服务器的特定行为                        |
| From                | 用户的电子邮箱地址                          |
| Host                | 请求资源所在服务器                          |
| If-Match            | 比较实体标记(ETag)                          |
| If-Modified-Since   | 比较资源的更新时间                          |
| If-None-Match       | 比较实体标记(与If-Match相反)                |
| If-Range            | 资源未更新时发送实体Byte的范围请求          |
| If-Unmodified-Since | 比较资源的更新时间(与If-Modified-Since相反) |
| Max-Forwards        | 最大传输逐跳帧                              |
| Proxy-Authorization | 代理服务器要求客户端的认证信息              |
| Range               | 实体的字节范围请求                          |
| Referer             |                                             |
| TE                  | 传输编码的优先级                            |
| User-Agent          | HTTP 客户端程序的信息                       |

##### 响应头

| 字段               | 含义                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推送资源创建经过时间         |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定URI      |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的实际要求     |
| Server             | HTTP 服务器的安装信息        |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

##### 实体头

即请求和响应头都可以携带的消息，用来标识该内容的具体内容，例如长度，能接受的长度之类。

<https://developer.mozilla.org/en-US/docs/Glossary/Entity_header>

| 字段             | 含义                   |
| ---------------- | ---------------------- |
| Allow            | 资源可支持的HTTP方法   |
| Content-Encoding | 实体主体使用的编码方式 |
| Content-Language | 实体主题自然语言       |
| Content-Length   | 实体主题大小           |
| Content-Location | 替代对应的资源URI      |
| Content-MD5      | 实体主题的报文摘要     |
| Content-Range    | 实体主题的位置范围     |
| Content-Type     | 实体主题的媒体范围     |
| Expires          | 实体主题过期的日期时间 |
| Last-Modified    | 资源最后修改日期时间   |

#### CentOS7下安装 nginx

##### 软件安装方式

同一个软件有很多种不同的安装方式，Linux操作系统中安装软件有几种常见方式：
1、源码编译安装：一般需要解压然后用make 、make install等命令，这种方式步骤比较复杂，编译时间长，而且结果不可控
2、RPM（RedHat Package Manager）是一个软件管理包，安装卸载变得简单了，但是无法解决软件包之间的依赖关系问题
3、YUM（Yellow dog Updater, Modified）是一个RPM的前端程序，可以自动解决软件的依赖关系。但是要注意版本的问题，默认从仓库中获取安装的不一定是最新版本
4、不需要安装只要配置环境变量的（解压就可以使用）

##### 下载

地址从`http://nginx.org/en/download.html`复制

```
cd /usr/local/soft
wget http://nginx.org/download/nginx-1.18.0.tar.gz
```

#####  解压

```
tar -xzvf nginx-1.18.0.tar.gz
```

#####  安装依赖环境

gcc环境：基本运行环境
pcre：用于nginx的http模块解析正则表达式
zlib：用户进行gzip压缩
openssl：用于nginx https协议的传输

```
yum install -y gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

#####  编译安装

–prefix=/usr/local/soft/nginx 的意思是把nginx安装到/usr/local/soft/nginx
所以后面会有一个源码目录nginx-1.18.0，一个编译安装后的目录nginx

```
cd /usr/local/soft/nginx-1.18.0
./configure --prefix=/usr/local/soft/nginx 
make && sudo make install
cd /usr/local/soft/nginx/
```

测试配置是否成功：

```
/usr/local/soft/nginx/sbin/nginx -t -c /usr/local/soft/nginx/conf/nginx.conf
```

#####  启动Nginx

```
/usr/local/soft/nginx/sbin/nginx
```

浏览器直接访问IP（HTTP协议默认80端口，不需要输入）：
![image.png](https://gper.club/server-img//mdEditors/2020/12/d1b6faabc6c14af3bb68eebfc1ec2792.png)

#####  copy vimfile

为了让VIM查看nginx配置文件时语法高亮，需要把相应文件copy到VIM目录。
先确定本机的vimfiles目录在哪个位置。

```
find / -name vimfiles
cd /usr/local/soft/nginx-1.18.0
cp -r contrib/vim/* /usr/share/vim/vimfiles/
```

#####  常用命令

```
nginx -s reopen #重启Nginx

nginx -s reload #重新加载Nginx配置文件，然后以优雅的方式重启Nginx

nginx -s stop #强制停止Nginx服务

nginx -s quit #优雅地停止Nginx服务（即处理完所有请求后再停止服务）

nginx -t #检测配置文件是否有语法错误，然后退出

nginx -?,-h #打开帮助信息

nginx -v #显示版本信息并退出

nginx -V #显示版本和配置选项信息，然后退出

nginx -t #检测配置文件是否有语法错误，然后退出

nginx -T #检测配置文件是否有语法错误，转储并退出

nginx -q #在检测配置文件期间屏蔽非错误信息

nginx -p prefix #设置前缀路径(默认是:/usr/share/nginx/)

nginx -c filename #设置配置文件(默认是:/etc/nginx/nginx.conf)

nginx -g directives #设置配置文件外的全局指令

killall nginx #杀死所有nginx进程
```


##### Nginx 配置文件

指定大全: http://nginx.org/en/docs/dirindex.html

##### 虚拟主机

大概的意思就是，当购买了一个较大空间的服务器的时候，可以出租给不同的想要部署项目的人，而这些项目可能并不是那么需要内存和CPU资源，例如一些发表文章的博客什么之类得。

这个时候，我们可以通过nginx来配置虚拟主机，当用户输入某个域名的时候，将会和nginx的配置文件上的`server_name`进行匹配这些域名和下面得资源。而我们要做的，只是在某个目录下给他们分配相应目录的使用权限即可，让他们上传部署自己的项目，然后通过nginx的映射，找到部署项目的入口。

```tiki wiki
[root@VM-0-15-centos nginx]# ls
client_body_temp  conf  fastcgi_temp  html  logs  proxy_temp  sbin  scgi_temp  uwsgi_temp
[root@VM-0-15-centos nginx]# mkdir data
[root@VM-0-15-centos nginx]# ll
total 40
drwx------ 2 nobody root 4096 Feb 28 15:07 client_body_temp
drwxr-xr-x 2 root   root 4096 Feb 28 15:32 conf
drwxr-xr-x 2 root   root 4096 Feb 28 23:11 data
drwx------ 2 nobody root 4096 Feb 28 15:07 fastcgi_temp
drwxr-xr-x 2 root   root 4096 Feb 28 15:06 html
drwxr-xr-x 2 root   root 4096 Feb 28 23:03 logs
drwx------ 2 nobody root 4096 Feb 28 15:07 proxy_temp
drwxr-xr-x 2 root   root 4096 Feb 28 15:06 sbin
drwx------ 2 nobody root 4096 Feb 28 15:07 scgi_temp
drwx------ 2 nobody root 4096 Feb 28 15:07 uwsgi_temp
## 我们可以随意在磁盘上创建希望给用户使用的磁盘目录，我们这里给直接在nginx的目录下面创建目录。
[root@VM-0-15-centos nginx]# cd data
[root@VM-0-15-centos data]# mkdir pop
[root@VM-0-15-centos data]# mkdir pipi
[root@VM-0-15-centos data]# mkdir bob
[root@VM-0-15-centos data]# ll
total 12
drwxr-xr-x 2 root root 4096 Feb 28 23:14 bob
drwxr-xr-x 2 root root 4096 Feb 28 23:14 pipi
drwxr-xr-x 2 root root 4096 Feb 28 23:14 pop
## 这样我们位 bob pipi pop 创建了属于他们自己的磁盘空间。
## 接着为了模拟一个他们自己部署了项目，我们为他们创建一个index.html页面，并写点什么，到时候访问的时候，方便获取到响应地部署信息。
[root@VM-0-15-centos bob]# cat index.html
I'M BOB
[root@VM-0-15-centos bob]# 
### 以上我们为每个文件夹都创建了属于他们自己的 index文件，文件内容都是他们的名字。
[root@VM-0-15-centos conf]# cp nginx.conf ./nginx-domains.conf
[root@VM-0-15-centos conf]# ll
total 72
-rw-r--r-- 1 root root 1077 Feb 28 15:06 fastcgi.conf
-rw-r--r-- 1 root root 1077 Feb 28 15:06 fastcgi.conf.default
-rw-r--r-- 1 root root 1007 Feb 28 15:06 fastcgi_params
-rw-r--r-- 1 root root 1007 Feb 28 15:06 fastcgi_params.default
-rw-r--r-- 1 root root 2837 Feb 28 15:06 koi-utf
-rw-r--r-- 1 root root 2223 Feb 28 15:06 koi-win
-rw-r--r-- 1 root root 5231 Feb 28 15:06 mime.types
-rw-r--r-- 1 root root 5231 Feb 28 15:06 mime.types.default
-rw-r--r-- 1 root root 2656 Feb 28 15:06 nginx.conf
-rw-r--r-- 1 root root 2686 Feb 28 15:32 nginx.conf.default
-rw-r--r-- 1 root root 2656 Feb 28 23:24 nginx-domains.conf
-rw-r--r-- 1 root root  636 Feb 28 15:06 scgi_params
-rw-r--r-- 1 root root  636 Feb 28 15:06 scgi_params.default
-rw-r--r-- 1 root root  664 Feb 28 15:06 uwsgi_params
-rw-r--r-- 1 root root  664 Feb 28 15:06 uwsgi_params.default
-rw-r--r-- 1 root root 3610 Feb 28 15:06 win-utf
## 我们对nginx-conf做一个备份，来做这个演示案例 nginx-domains.conf
```

其它部分直接复制原本的配置，只是加了几个映射

```properties

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
    
   # 用来演示不同域名之间的转发


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    server {
        listen       80;
        server_name  www.pop.com;
	# 根目录所在位置
		root    /usr/local/soft/nginx/data/pop;
        location / {
            index  index.html index.htm;
        }
    }
    
    server {
        listen	     80;
		server_name  www.pipi.com;
		root   /usr/local/soft/nginx/data/pipi;
		location / {
             index index.html index.htm;
		}
    }	

    server {
        listen       80;
		server_name www.bob.com
		root /usr/local/soft/nginx/data/bob;
		location / {
             index index.html index.htm;
		}
      
    }



    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

为此，我们还需要更改一下域名，由于正确的域名解析流程需要通过dns域名解析服务器得到ip地址才可以完成，所以我们这边直接通过修改host文件的方式直接获得地址。

```
# 直接映射路径 这里写目标地址的IP地址
115.XXX.XXX.127  www.pop.com www.pipi.com www.bob.com
```

准备就绪后，我们就可以启动nginx了。在此之前为了更方便的启动，我们可以起别名。

```tiki wiki
# 查看别名的指令
[root@VM-0-15-centos conf]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
# 编辑 ~表示当前用户，的方言指定
# 格式为 alias [命令]=[对应的文件命令1];[对应的文件命令2]
# 多个不同指令可以用 分号(;)隔开
[root@VM-0-15-centos conf]# vim ~/.bashrc
# 修改完后，重新编译下
[root@VM-0-15-centos conf]# source ~/.bashrc
# 启动 nginx,是使用对应的配置文件
[root@VM-0-15-centos sbin]# ./nginx -c /usr/local/soft/nginx/conf/nginx-domains.conf 
```

下面就是Nginx实现虚拟主机的效果

![1614527573638](./img/1614527573638.png)

![1614527520823](./img/1614527520823.png)

![1614527535334](./img/1614527535334.png)

如果之后的配置文件多了起来，我们还可以使用http模块下的inculde功能，或者用通配符，包含这些文件配置。

```properties
http {
	# 就是这样
    include       /usr/local/soft/nginx/conf/vhost/*.conf;
    default_type  application/octet-stream;
```

有了这个，我们甚至可以用来映射子目录之类的，子域名。例如我希望在pop文件夹下再创建一个blob来放置博客的网址，所以我们可以映射子域名成这样 blob.pop.com。

#### Nginx 的反向代理

首先说正向代理，由于国内环境，有些网站我们是上不了的，因为此路不同，所以我们必须绕路，而因为有些路是可以走到我们需要的网站，所以我们可以通过走这些路来达到我们的目的地。这就是我们的正向代理，也就是我们常说的科学上网，我们通过可以上这些网站的ip地址，来请求目标地址，然后返回数据到正向代理，接着代理再把数据发给我们，达到访问的目的。

![1614604117726](./img/1614604117726.png)

所以，**正向代理是运行在客户端得程序**。

那么**反向代理，顾名思义，就是运行在服务端的程序**。

![1614605393722](./img/1614605393722.png)

##### 关于反向代理的配置

由于反向代理是代理服务器，反向代理的效果就是，我请求一个代理方的地址，他会帮我想目标地址请求地址。

我们可以创建一个springboot项目，然后创建一个地址，随便什么地址，只要能够保证访问就行。

```java
@RestController
public class NginxController{
    @RequestMapping("user/query")
    public String query(HttpSerlvetRequest request){
        return "nice";
    }
}
```

假设我们现在这个springboot所在的地址是`192.168.44.1:9096`，也就是说我们使用`192.168.44.1:9096/user/query`页面上就会返回nice字样。但是因为某些原因，以上的地址我们使用让我们无法访问。这个时候，我们的反向代理，也就是nginx所在地址是`192.168.44.181`，而nginx可以访问`192.168.44.1`的服务，所以我们希望在网址上输入`192.168.44.1/user/query`,虽然不是真正服务器的地址，但是还是会返回nice字样。

我们回到nginx的地方进行配置。

```properties
server {
        listen       80;
        # nginx 所在的位置，只要服务器和这个匹配就会进入这个server块
        server_name  192.168.44.1;
        location / {
        # 非常重要的属性，用于表示被代理的地址，反向代理的位置，所有的请求都会被转发到这个地址
            proxy_pass http://192.168.44.1:9096;
        }
    }

```

启动nginx即可。

```properties
location / {
	// location 的路由规则    
}
```

| 模式                 | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| location = /uri      | = 表示精准匹配，只有完全匹配上才能生效                       |
| location ^~ /uri     | ^~ 开头对URL路径进行前缀匹配，并且在正则之前                 |
| localtion ~pattern   | 开头表示区分大小写的正则匹配                                 |
| localtion ~* pattern | 开头表示不区别大小写的正则表达式                             |
| location /uri        | 不带任何修饰符，也表示前缀匹配，但是在正则匹配之后           |
| localtion /          | 通用匹配，任何匹配到其他location的请求都会匹配到，相当于switch 中的 default |

优先级从高到底。

所以其实就是nginx帮你发一个请求而已。所以在nginx帮你转发请求的时候，其实可以做一些特别的事情。

例如nginx可以帮你把一个get请求改变成post请求。比如你发送给nginx服务器是一个get请求，只需要在server块中加上这样一句话，就可以改成post请求。

```properties
server {
        listen       80;
        # nginx 所在的位置，只要服务器和这个匹配就会进入这个server块
        server_name  192.168.44.1;
        location / {
        # 非常重要的属性，用于表示被代理的地址，反向代理的位置，所有的请求都会被转发到这个地址
            proxy_pass http://192.168.44.1:9096;
            # 修改请求类型
            proxy_method POST;
        }
    }
```

本质上，我们作为客户端想nginx发送了一次请求，然后nginx作为客户端再发送了一次请求，那么这里就会有一个问题，由于到真正服务器的上是由nginx发送的，所以会丢失一些我们作为真正发送者的信息，所以我们希望在nginx转发的时候，能够保存我们原始的信息。

```properties
server {
        listen       80;
        # nginx 所在的位置，只要服务器和这个匹配就会进入这个server块
        server_name  192.168.44.1;
        location / {
        # 非常重要的属性，用于表示被代理的地址，反向代理的位置，所有的请求都会被转发到这个地址
            proxy_pass http://192.168.44.1:9096;
            # 修改请求类型
            proxy_method POST;
            # 前面是变量名，后面是值
            # host地址
            proxy_set_header Host $host;
            # 调用地址
            proxy_set_header X-Real-IP $remote_addr;
            # ip列表
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
```

我们可以通过自定义的变量来存储这些值。$开头是nginx内置的变量。

到服务端获取。

```java
@RequestMapping("user/query")
public String query(HttpServletRequest request){
    //从request header 中获取
    System.out.println("客户端IP"+request.getHeader("X-Real-IP"));
    System.out.println("X-Forwarded-For"+request.getHeader("X-Forwaded-For"));
}
```

