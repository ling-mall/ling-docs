# Nginx

官网：[https://nginx.org](https://nginx.org)

文档：[https://nginx.org/en/docs/](https://nginx.org/en/docs/%E2%80%B8)

## 基础

默认目录：

* `/etc/nginx/`：配置文件目录
* `/etc/nginx/nginx.conf`：Nginx主配置文件
* `/etc/nginx/conf.d/`：额外的配置文件目录，可以通过 `include` 指令引入到 `nginx.conf` 中。
* `/var/log/nginx/`：日志文件目录（包括访问日志和错误日志）
* `/usr/share/nginx/`：网站文件目录（存放静态文件或动态脚本文件）
* `/usr/sbin/nginx`：Nginx 可执行文件

命令：

```shell
# 显示 NGINX 帮助菜单
nginx -h

# 显示 NGINX 版本
nginx -v

# 显示 NGINX 版本、build 信息和配置参数
nginx -V

# 测试 NGINX 配置，验证配置文件是否可用
nginx -t

# 测试 NGINX 配置并将验证后的配置打印到屏幕上
nginx -T

# 向正在运行的Nginx进程发送信号
nginx -s [stop|quit|reload|reopen]

# 停止Nginx进程
nginx -s stop

# 优雅地停止Nginx进程
nginx -s quit

# 重新加载Nginx配置文件
nginx -s reload

# 重新打开Nginx日志文件
nginx -s reopen
```

## 静态资源

简单示例

```config
server {
    # 监听80端口，并将其标记为默认服务器
    listen 80 default_server; 
    # 响应来自www.example.com的请求 
    server_name www.example.com;  

    location / {
        # 当请求匹配根URL时，在这个目录中查找文件
        root /usr/share/nginx/html;
        # 作为替代方案，可以使用alias指令  
        # alias /usr/share/nginx/html/;
        # 确定默认的索引文件  
        index index.html index.htm;  
    }
}
```

`default_server` 是 Nginx 配置中的一个特殊标识，用于表示默认服务器。当一个请求到达 Nginx 时，如果请求的域名在配置中找不到匹配的 server_name，那么就会使用被标记为 default_server 的配置块来处理这个请求。

`location /` 表示当请求匹配根URL时应该做的操作

`root` 指令指定了请求的根目录，即请求的 URL 匹配到该指令指定的目录时，Nginx 会在该目录中寻找文件。

`alias` 指令也是指定请求的文件路径，跟 root 的区别是：root是拼接，alias是替换。alias 会将 location 后面的 uri替换为 alias 指令指定的路径

`index` 指令指定了当请求的 URL 匹配到目录时，Nginx 应该返回哪个文件作为响应。

## 配置概要

```config
# 全局配置项
user              nginx;
worker_processes  auto;

# events 块
events {
    worker_connections  1024;
}

# http 块
http {
    # 全局配置项
    include       mime.types;
    default_type  application/octet-stream;

    # 服务器块
    server {
        # 服务器配置项
    }

    # 可以包含更多的 server 块
}

# stream 块
stream {
    # 服务器块
    server {
        # 服务器配置项
    }

    # 可以包含更多的 server 块
}
```

块：

* `events`：配置与事件相关的设置。
* `http`：配置与 HTTP 相关的设置。
* `stream`：配置与流媒体服务器相关的设置。

## 配置示例

来自：[https://www.cnblogs.com/54chensongxia/p/12938929.html](https://www.cnblogs.com/54chensongxia/p/12938929.html)

```config
######Nginx配置文件nginx.conf中文详解#####

#定义Nginx运行的用户和用户组
user www www;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;
 
#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /usr/local/nginx/logs/error.log info;

#进程pid文件
pid /usr/local/nginx/logs/nginx.pid;

#指定进程可以打开的最大描述符：数目
#工作模式与连接数上限
#这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。
#现在在linux 2.6内核下开启文件打开数为65535，worker_rlimit_nofile就相应应该填写65535。
#这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。
worker_rlimit_nofile 65535;


events
{
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型
    #是Linux 2.6以上版本内核中的高性能网络I/O模型，linux建议epoll，如果跑在FreeBSD上面，就用kqueue模型。
    #补充说明：
    #与apache相类，nginx针对不同的操作系统，有不同的事件模型
    #A）标准事件模型
    #Select、poll属于标准事件模型，如果当前系统不存在更有效的方法，nginx会选择select或poll
    #B）高效事件模型
    #Kqueue：使用于FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 MacOS X.使用双处理器的MacOS X系统使用kqueue可能会造成内核崩溃。
    #Epoll：使用于Linux内核2.6版本及以后的系统。
    #/dev/poll：使用于Solaris 7 11/99+，HP/UX 11.22+ (eventport)，IRIX 6.5.15+ 和 Tru64 UNIX 5.1A+。
    #Eventport：使用于Solaris 10。 为了防止出现内核崩溃的问题， 有必要安装安全补丁。
    use epoll;

    #单个进程最大连接数（最大连接数=连接数*进程数）
    #根据硬件调整，和前面工作进程配合起来用，尽量大，但是别把cpu跑到100%就行。每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为。
    worker_connections 65535;

    #keepalive超时时间。
    keepalive_timeout 60;

    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求头的大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。
    #分页大小可以用命令getconf PAGESIZE 取得。
    #[root@web001 ~]# getconf PAGESIZE
    #4096
    #但也有client_header_buffer_size超过4k的情况，但是client_header_buffer_size该值必须设置为“系统分页大小”的整倍数。
    client_header_buffer_size 4k;

    #这个将为打开文件指定缓存，默认是没有启用的，max指定缓存数量，建议和打开文件数一致，inactive是指经过多长时间文件没被请求后删除缓存。
    open_file_cache max=65535 inactive=60s;

    #这个是指多长时间检查一次缓存的有效信息。
    #语法:open_file_cache_valid time 默认值:open_file_cache_valid 60 使用字段:http, server, location 这个指令指定了何时需要检查open_file_cache中缓存项目的有效信息.
    open_file_cache_valid 80s;

    #open_file_cache指令中的inactive参数时间内文件的最少使用次数，如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个文件在inactive时间内一次没被使用，它将被移除。
    #语法:open_file_cache_min_uses number 默认值:open_file_cache_min_uses 1 使用字段:http, server, location  这个指令指定了在open_file_cache指令无效的参数中一定的时间范围内可以使用的最小文件数,如果使用更大的值,文件描述符在cache中总是打开状态.
    open_file_cache_min_uses 1;
  
    #语法:open_file_cache_errors on | off 默认值:open_file_cache_errors off 使用字段:http, server, location 这个指令指定是否在搜索一个文件时记录cache错误.
    open_file_cache_errors on;
}
 
 
 
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http
{
    #文件扩展名与文件类型映射表
    include mime.types;

    #默认文件类型
    default_type application/octet-stream;

    #默认编码
    #charset utf-8;

    #服务器名字的hash表大小
    #保存服务器名字的hash表是由指令server_names_hash_max_size 和server_names_hash_bucket_size所控制的。参数hash bucket size总是等于hash表的大小，并且是一路处理器缓存大小的倍数。在减少了在内存中的存取次数后，使在处理器中加速查找hash表键值成为可能。如果hash bucket size等于一路处理器缓存的大小，那么在查找键的时候，最坏的情况下在内存中查找的次数为2。第一次是确定存储单元的地址，第二次是在存储单元中查找键 值。因此，如果Nginx给出需要增大hash max size 或 hash bucket size的提示，那么首要的是增大前一个参数的大小.
    server_names_hash_bucket_size 128;

    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求的头部大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。分页大小可以用命令getconf PAGESIZE取得。
    client_header_buffer_size 32k;

    #客户请求头缓冲大小。nginx默认会用client_header_buffer_size这个buffer来读取header值，如果header过大，它会使用large_client_header_buffers来读取。
    large_client_header_buffers 4 64k;

    #设定通过nginx上传文件的大小
    client_max_body_size 8m;

    #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
    #sendfile指令指定 nginx 是否调用sendfile 函数（zero copy 方式）来输出文件，对于普通应用，必须设为on。如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络IO处理速度，降低系统uptime。
    sendfile on;

    #开启目录列表访问，合适下载服务器，默认关闭。
    autoindex on;

    #此选项允许或禁止使用socke的TCP_CORK的选项，此选项仅在使用sendfile的时候使用
    tcp_nopush on;
   
    tcp_nodelay on;

    #长连接超时时间，单位是秒
    keepalive_timeout 120;

    #FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    #gzip模块设置
    gzip on; #开启gzip压缩输出
    gzip_min_length 1k;    #最小压缩文件大小
    gzip_buffers 4 16k;    #压缩缓冲区
    gzip_http_version 1.0;    #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    gzip_comp_level 2;    #压缩等级
    gzip_types text/plain application/x-javascript text/css application/xml;    #压缩类型，默认就已经包含textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    gzip_vary on;

    #开启限制IP连接数的时候需要使用
    #limit_zone crawler $binary_remote_addr 10m;



    #负载均衡配置
    upstream jh.w3cschool.cn {
   
        #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
        server 192.168.80.121:80 weight=3;
        server 192.168.80.122:80 weight=2;
        server 192.168.80.123:80 weight=3;

        #nginx的upstream目前支持4种方式的分配
        #1、轮询（默认）
        #每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
        #2、weight
        #指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
        #例如：
        #upstream bakend {
        #    server 192.168.0.14 weight=10;
        #    server 192.168.0.15 weight=10;
        #}
        #2、ip_hash
        #每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
        #例如：
        #upstream bakend {
        #    ip_hash;
        #    server 192.168.0.14:88;
        #    server 192.168.0.15:80;
        #}
        #3、fair（第三方）
        #按后端服务器的响应时间来分配请求，响应时间短的优先分配。
        #upstream backend {
        #    server server1;
        #    server server2;
        #    fair;
        #}
        #4、url_hash（第三方）
        #按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。
        #例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
        #upstream backend {
        #    server squid1:3128;
        #    server squid2:3128;
        #    hash $request_uri;
        #    hash_method crc32;
        #}

        #tips:
        #upstream bakend{#定义负载均衡设备的Ip及设备状态}{
        #    ip_hash;
        #    server 127.0.0.1:9090 down;
        #    server 127.0.0.1:8080 weight=2;
        #    server 127.0.0.1:6060;
        #    server 127.0.0.1:7070 backup;
        #}
        #在需要使用负载均衡的server中增加 proxy_pass http://bakend/;

        #每个设备的状态设置为:
        #1.down表示单前的server暂时不参与负载
        #2.weight为weight越大，负载的权重就越大。
        #3.max_fails：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream模块定义的错误
        #4.fail_timeout:max_fails次失败后，暂停的时间。
        #5.backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

        #nginx支持同时设置多组的负载均衡，用来给不用的server来使用。
        #client_body_in_file_only设置为On 可以讲client post过来的数据记录到文件中用来做debug
        #client_body_temp_path设置记录文件的目录 可以设置最多3层目录
        #location对URL进行匹配.可以进行重定向或者进行新的代理 负载均衡
    }
   
   
   
    #虚拟主机的配置
    server
    {
        #监听端口
        listen 80;

        #域名可以有多个，用空格隔开
        server_name www.w3cschool.cn w3cschool.cn;
        index index.html index.htm index.php;
        root /data/www/w3cschool;

        #对******进行负载均衡
        location ~ .*.(php|php5)?$
        {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
     
        #图片缓存时间设置
        location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires 10d;
        }
     
        #JS和CSS缓存时间设置
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
     
        #日志格式设定
        #$remote_addr与$http_x_forwarded_for用以记录客户端的ip地址；
        #$remote_user：用来记录客户端用户名称；
        #$time_local： 用来记录访问时间与时区；
        #$request： 用来记录请求的url与http协议；
        #$status： 用来记录请求状态；成功是200，
        #$body_bytes_sent ：记录发送给客户端文件主体内容大小；
        #$http_referer：用来记录从那个页面链接访问过来的；
        #$http_user_agent：记录客户浏览器的相关信息；
        #通常web服务器放在反向代理的后面，这样就不能获取到客户的IP地址了，通过$remote_add拿到的IP地址是反向代理服务器的iP地址。反向代理服务器在转发请求的http头信息中，可以增加x_forwarded_for信息，用以记录原有客户端的IP地址和原来客户端的请求的服务器地址。
        log_format access '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';
     
        #定义本虚拟主机的访问日志
        access_log  /usr/local/nginx/logs/host.access.log  main;
        access_log  /usr/local/nginx/logs/host.access.404.log  log404;
     
        #对 "/" 启用反向代理
        location / {
            proxy_pass http://127.0.0.1:88;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
         
            #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         
            #以下是一些反向代理的配置，可选。
            proxy_set_header Host $host;

            #允许客户端请求的最大单文件字节数
            client_max_body_size 10m;

            #缓冲区代理缓冲用户端请求的最大字节数，
            #如果把它设置为比较大的数值，例如256k，那么，无论使用firefox还是IE浏览器，来提交任意小于256k的图片，都很正常。如果注释该指令，使用默认的client_body_buffer_size设置，也就是操作系统页面大小的两倍，8k或者16k，问题就出现了。
            #无论使用firefox4.0还是IE8.0，提交一个比较大，200k左右的图片，都返回500 Internal Server Error错误
            client_body_buffer_size 128k;

            #表示使nginx阻止HTTP应答代码为400或者更高的应答。
            proxy_intercept_errors on;

            #后端服务器连接的超时时间_发起握手等候响应超时时间
            #nginx跟后端服务器连接超时时间(代理连接超时)
            proxy_connect_timeout 90;

            #后端服务器数据回传时间(代理发送超时)
            #后端服务器数据回传时间_就是在规定时间之内后端服务器必须传完所有的数据
            proxy_send_timeout 90;

            #连接成功后，后端服务器响应时间(代理接收超时)
            #连接成功后_等候后端服务器响应时间_其实已经进入后端的排队之中等候处理（也可以说是后端服务器处理请求的时间）
            proxy_read_timeout 90;

            #设置代理服务器（nginx）保存用户头信息的缓冲区大小
            #设置从被代理服务器读取的第一部分应答的缓冲区大小，通常情况下这部分应答中包含一个小的应答头，默认情况下这个值的大小为指令proxy_buffers中指定的一个缓冲区的大小，不过可以将其设置为更小
            proxy_buffer_size 4k;

            #proxy_buffers缓冲区，网页平均在32k以下的设置
            #设置用于读取应答（来自被代理服务器）的缓冲区数目和大小，默认情况也为分页大小，根据操作系统的不同可能是4k或者8k
            proxy_buffers 4 32k;

            #高负荷下缓冲大小（proxy_buffers*2）
            proxy_busy_buffers_size 64k;

            #设置在写入proxy_temp_path时数据的大小，预防一个工作进程在传递文件时阻塞太长
            #设定缓存文件夹大小，大于这个值，将从upstream服务器传
            proxy_temp_file_write_size 64k;
        }
     
     
        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status on;
            access_log on;
            auth_basic "NginxStatus";
            auth_basic_user_file confpasswd;
            #htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
        }
     
        #本地动静分离反向代理配置
        #所有jsp的页面均交由tomcat或resin处理
        location ~ .(jsp|jspx|do)?$ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8080;
        }
     
        #所有静态文件由nginx直接读取不经过tomcat或resin
        location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|
        pdf|xls|mp3|wma)$
        {
            expires 15d; 
        }
     
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
    }
}
######Nginx配置文件nginx.conf中文详解#####
```

## 全局配置

1. `user`

   用于配置运行Nginx服务器的worker进程的用户和用户组，默认值为 `nobody`

   语法：`user user [group]`

   需要确保指定的用户和用户组已存在，
2. `master_process`

   用于配置是否以主进程的方式运行Nginx服务器，默认值为 on

   语法：`master_process on | off`
3. `worker_processes`

   用于配置Nginx服务器的worker进程数，默认值为 1

   语法：`worker_processes number | auto`

   该指令可以接受一个整数参数，表示要启动的worker进程数。如果不指定该参数，Nginx将会根据CPU的核数自动设置worker进程数。

   该指令也可以接受一个 `auto`参数，表示根据CPU的核数自动设置worker进程数。
4. `error_log`

   用于配置Nginx服务器的错误日志文件，默认值为 `/var/log/nginx/error.log`

   语法：`error_log path [level]`

   该指令可以接受两个参数，第一个参数为日志文件路径，第二个参数为日志级别，默认为notice级别。
5. `pid`

   用于配置Nginx服务器的进程ID文件，默认值为 `/var/run/nginx.pid`

   语法：`pid path`

   该指令可以接受一个参数，为进程ID文件路径。
6. `include`

   用于引入其他配置文件

   语法：`include files`

   该指令可以接受一个或多个参数，为要引入的文件列表。
7. `daemon`

   用于配置是否以守护进程的方式运行Nginx服务器，默认值为 off

   语法：`daemon on | off`

   该指令可以接受一个参数，为 on 或 off，表示是否以守护进程的方式运行Nginx服务器。
8. `worker_rlimit_nofile`

   用于配置worker进程可以打开的最大文件描述符数，默认值为 1024

   语法：`worker_rlimit_nofile number`

   该指令可以接受一个整数参数，表示worker进程可以打开的最大文件描述符数。
9. `worker_shutdown_timeout`

   用于配置worker进程在关闭连接之前等待的最大秒数，默认值为 10

   语法：`worker_shutdown_timeout seconds`

   该指令可以接受一个整数参数，表示worker进程在关闭连接之前等待的最大秒数。
10. `worker_cpu_affinity`

    用于配置worker进程的CPU亲和性，默认值为 `auto`

    语法：`worker_cpu_affinity cpumask`

    例子：

    ```config
    worker_processes     4;
    worker_cpu_affinity 0001 0010 0100 1000;
    ```

    解释：0001表示启用第一个CPU内核，0010表示启用第二个CPU内核，依此类推

    该指令可以接受一个整数参数，表示worker进程的CPU亲和性。
11. `worker_priority`

    用于配置worker进程的优先级，默认值为 `auto`

    语法：`worker_priority priority`

    该指令可以接受一个整数参数，表示worker进程的优先级。

## events 块

在Nginx的主配置文件中，events部分用于配置Nginx服务器的事件模块相关参数，控制Nginx服务器在处理连接请求时的行为。

1. `worker_connections`

   用于配置每个worker进程可以处理的最大并发连接数，默认值为 1024

   语法：`worker_connections number`

   该指令可以接受一个整数参数，表示每个worker进程可以处理的最大并发连接数。
2. `multi_accept`

   用于配置是否允许同时接受多个连接，默认值为 on

   语法：`multi_accept on | off`

   该指令可以接受一个参数，为 on 或 off，表示是否允许同时接受多个连接。
3. `use`

   指定Nginx使用的事件模块。常见的事件模块有"epoll"、"kqueue"和"eventport"等。

   语法：`use module`

   该指令可以接受一个或多个参数，为要引入的模块列表。
4. `accept_mutex`

   用于配置是否使用互斥锁来保护共享的accept队列，默认值为 on

   语法：`accept_mutex on | off`

   该指令可以接受一个参数，为 on 或 off，表示是否使用互斥锁来保护共享的accept队列。

   如果开启互斥锁，工作进程会轮流地接受新的连接。否则，所有的工作进程都会得到这个连接的通知，如果连接数量很低，一些（空闲的）工作进程就会无意义地消耗系统资源(cpu)。
5. `accept_mutex_delay`

   用于配置互斥锁的延迟时间，默认值为 500ms

   语法：`accept_mutex_delay time`

   该指令可以接受一个整数参数，表示互斥锁的延迟时间。

## http 块

在Nginx的主配置文件中，http部分用于配置Nginx服务器的HTTP模块相关参数，控制Nginx服务器在处理HTTP请求时的行为。

1. `include`

   用于引入其他配置文件，通常用于加载 MIME 类型配置文件

   语法：`include files`

   一般是 ：`include mime.types`，里面有绝大多数允许的类型，还可以导入所有 `conf.d` 目录下的配置文件。

   ```config
   # 常见的 MIME 类型配置文件
   include mime.types;
   # 导入所有 conf.d 目录下的配置文件。
   include /etc/nginx/conf.d/*.conf;
   ```

   可以有多个 `include` ，路径是相对路径
2. `default_type`

   用于配置默认的文件类型，默认值为 `application/octet-stream`

   语法：`default_type type`
3. `access_log`

   用于配置Nginx服务器的访问日志文件，默认值为 `/var/log/nginx/access.log`

   语法：`access_log path [format]`

   该指令可以接受两个参数，第一个参数为日志文件路径，第二个参数为日志格式，默认为common。
4. `sendfile`

   用于配置是否使用 sendfile 函数，默认值为 on

   语法：`sendfile on | off`

   该指令可以接受一个参数，为 on 或 off，表示是否使用sendfile函数。

   如果开启sendfile，Nginx会使用sendfile函数，从而提高文件传输效率。
5. `tcp_nopush`

   用于配置是否使用TCP_CORK选项，默认值为 on

   语法：`tcp_nopush on | off`

   开启了sendfile的情况下，合并请求后统一发送给客户端。
6. `tcp_nodelay`

   用于配置是否使用TCP_NODELAY选项，默认值为 on

   语法：`tcp_nodelay on | off`

   该指令可以接受一个参数，为 on 或 off，表示是否使用TCP_NODELAY选项。

   如果开启TCP_NODELAY，Nginx会在发送响应数据时，当为off时，延迟0.2s发送，默认On时，不延迟发送，立即发送用户响应报文。
7. `keepalive_timeout`

   用于配置客户端连接的超时时间，默认值为 65s

   语法：`keepalive_timeout time`

   该指令可以接受一个整数参数，表示客户端连接的超时时间。
8. `keepalive_requests`

   用于配置客户端连接的最大请求数，默认值为 1024

   语法：`keepalive_requests number`

### server 块

在Nginx的主配置文件中，server块用于配置Nginx服务器的虚拟主机相关参数，控制Nginx服务器在处理HTTP请求时的行为。

1. `server_name`

   用于配置虚拟主机的域名，可以有多个，默认值为 `*`

   语法：`server_name name [name...]`

   支持通配符与正则，文档 [https://nginx.org/en/docs/http/server_names.html](https://nginx.org/en/docs/http/server_names.html)
2. `listen`

   用于配置监听的端口，可以有多个，默认值为 `80`

   语法：`listen IP[:PORT]`

   可使用 `default_server` 表示默认服务器。当一个请求到达Nginx时，如果请求的域名在配置中找不到匹配的 `server_name`，那么就会使用被标记为 `default_server` 的配置块来处理这个请求。

   ```config
   server {
       listen 80;
       server_name www.example.com;
      ...
   }

   server {
       listen 80 default_server;
      ...
   }
   ```

   ```config
   # 监听具体IP和具体端口上的连接
   listen 192.168.31.177:8080;
   # 监听IP上所有端口上的连接
   listen 192.168.31.177;
   # 监听具体端口上的所有IP的连接
   listen 8080;
   ```

3. `index`

   用于配置默认的首页文件，一般为 `index.html index.htm index.php`

   语法：`index file [file...]`

   示例：

   ```config
   server {
       root /usr/share/nginx/html;
       index index.html index.htm index.php;
   }
   ```

4. `location`

   用于配置请求的路径

   语法：`location [=|~|~*|^~] /uri/ {... }`

   该指令可以接受一个或多个参数，第一个参数为匹配模式，后面的参数为配置块。

   匹配模式：

   * `=`：用于标准uri前，需要请求字串与uri精确匹配，大小敏感,如果匹配成功就停止向下匹配并立即处理请求
   * `~`：用于标准uri前，表示包含正则表达式,并且区分大小写
   * `~*`：用于标准uri前，表示包含正则表达式,并且不区分大写
   * `^~`：用于标准uri前，表示包含正则表达式,并且匹配以指定的正则表达式开头,对URI的最左边部分做匹配检查，不区分字符大小写
   * 不带符号 : 不带符号默认是大小写敏感，前缀匹配，相当于加了“~”与“^~”
   * `@` : nginx内部跳转
5. `location.deny`

   用于配置拒绝访问某个路径，可以拒绝所有请求，也可以拒绝指定 ip

   语法：`deny [all | ip]`

   示例：

   ```config
   server {
       # 禁止所有请求
       location /a/ {
           deny all;
       }

       # 禁止指定IP的请求
       location /admin/ {
           deny 127.0.0.1;
       }
   }
   ```

6. `location.allow`

   用于配置允许访问某个路径，可以允许所有请求，也可以允许指定 ip

   语法：`allow [all | ip]`

   示例：

   ```config
   server {
       location /a/ {
           # 允许指定IP的请求
           allow 192.168.192.150;
           deny all;
       }

       location /admin/ {
           # 允许特定IP地址段的访问
           allow 192.168.2.0/24;
           deny all;
       }
   }
   ```

   ```config
   # 仅允许特定IP地址段的GET请求访问特定URI模式
   location ~ ^/api/ {
       if ($request_method != GET) {
           return 403;
       }
       allow 192.168.1.0/24;
       deny all;
   }
   ```

7. `location.alias`

   alias 用于创建一个路径别名的指令。 别名可以用于将文件或目录从一个位置映射到另一个位置，提供更灵活的访问控制

   语法：`alias path`

   ```config
   location /dist/ {
       alias /usr/share/nginx/html;
   }
   ```

   当请求 `/dist/static/css/style.css` 时，nginx 会将其映射到 `/usr/share/nginx/html/static/css/style.css`
8. `http.root | server.root | location.root`

   这个指令用于设置请求寻找资源的跟目录，此指令可以在http块、server块或者location块中配置。由于使用Nginx服务器多数情况下要配置多个location块对不同的请求分别做出处理，因此该指令通常在location块中进行设置。

   用于配置虚拟主机的根目录，默认值为 `/usr/share/nginx/html`

   语法：`root path`

   该指令可以接受一个参数，为要设置的根目录。

   ```config
   location /dist/ {
       root /usr/share/nginx/html;
   }
   ```

   当请求 `/dist/static/css/style.css` 时，nginx 会将其映射到 `/usr/share/nginx/html/dist/static/css/style.css` 文件上。
9. `location.alias`

   也是指定请求的文件路径，跟 root 的区别是：root是拼接，alias是替换。alias 会将 location 后面的 uri 替换为 alias 指令指定的路径。

   语法：`alias path`

   ```config
   location /dist/ {
       alias /usr/share/nginx/html;
   }
   ```

   当请求 `/dist/static/css/style.css` 时，nginx 会将其映射到 `/usr/share/nginx/html/static/css/style.css` 文件上
10. `location.try_files`

    用于配置 Nginx 尝试访问的文件列表，当请求的文件不存在时，Nginx 会依次尝试访问列表中的文件，并返回第一个存在的文件。

    语法：`try_files file [file...]`

    示例：

    ```config
    location / {
        try_files $uri $uri/ /index.html;
    }
    ```

    当请求 `/test.html` 时，Nginx 会尝试访问 `/test.html`、`/test.html/`、`/index.html` 三个文件，并返回第一个存在的文件。

    ```config
    location / {
        try_files $uri $uri/ =404;
    }
    ```

    当请求 `/test.html` 时，Nginx 会尝试访问 `/test.html`、`/test.html/`，如果仍然找不到文件，则返回 404 响应码。

## 反向代理

反向代理（Reverse Proxy）是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

当使用 Nginx 的反向代理时，用户的整个请求会在nginx中缓冲直至传送给后端被代理的服务器.因此,上传进度的测算就会运作得不正确,如果它们通过测算后端服务器收到的数据来工作的话

以下所有配置都在 server 的 location 块中进行配置

1. `proxy_pass`

   用于配置反向代理的目标地址

   语法：`proxy_pass url`

   示例：

   ```config
   location /api/ {
       proxy_pass http://127.0.0.1:8080;
   }
   ```

   该指令用于配置反向代理的目标地址，url 即为目标地址。

   nginx 中 location 和 proxy_pass 中最后是否存在反斜杠，会对最终服务器收到的请求路径产生影响。

   若proxy_pass 后加 `/`，代表去除掉请求和 location 的匹配的字符串，然后追加到 proxy_pass 后面的地址后面，不加 `/` 则与location 的匹配的字符串全部追加到地址后面。
2. `proxy_redirect`

   用于配置反向代理的重定向地址，用于在反向代理服务器中修改响应头中的 Location 和 Refresh 字段。它的作用是将后端服务器返回的重定向 URL 中的域名或路径进行替换。

   语法：`proxy_redirect [ default|off|redirect replacement ]`

   示例：

   ```config
   location /api/ {
       proxy_redirect http://backend-server:8000/ http://frontend-server/;
   }
   ```

   该配置将后端服务器返回的重定向 URL 中的 `http://backend-server:8000/` 替换为 `http://frontend-server/`
3. `proxy_set_header`

   用于配置反向代理的请求头信息

   语法：`proxy_set_header field value`

   示例：

   ```config
       location ^~ /api/ {
               proxy_pass http://localhost:8080/;
               proxy_set_header   Host             $host;
               proxy_set_header   X-Real-IP        $remote_addr;
               proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
       }
   ```

   以上配置通常一起使用，以便在代理请求中传递客户端的真实 IP 地址和所有经过的代理服务器 IP 地址。
4. `proxy_set_body`

   用于配置反向代理的请求主体信息

   语法：`proxy_set_body value`

   示例：

   ```config
   location /api/ {
       proxy_pass http://127.0.0.1:8080;
       proxy_set_body 'hello world';
   }
   ```

5. `proxy_buffering`

   用于配置反向代理的缓冲区是否开启

   语法：`proxy_buffering on|off`

   该指令用于配置反向代理的缓冲区是否开启，默认为 on
6. `proxy_buffer_size`

   用于配置反向代理的缓冲区大小

   语法：`proxy_buffer_size size`
7. `proxy_buffers`

   设置缓冲区的大小和数量

   语法：`proxy_buffers the_number is_size;`

   默认值：`proxy_buffers 8 4k/8k;`
8. `proxy_busy_buffers_size`

   当nginx还在读取被代理服务器的数据响应的同时间一次性向客户端响应的数据的最大为多少

   语法：`proxy_busy_buffers_size size`

   默认值: `proxy_busy_buffers_size proxy_buffer_size * 2;`

## 全局变量

`$args` ：这个变量等于请求行中的参数。

`$content_length` ：请求头中的Content-length字段。

`$content_type` ：请求头中的Content-Type字段。

`$document_root` ：当前请求在root指令中指定的值。

`$host` ：请求主机头字段，否则为服务器名称。

`$http_user_agent` ：客户端agent信息

`$http_cookie` ：客户端cookie信息

`$limit_rate` ：这个变量可以限制连接速率。

`$request_body_file` ：客户端请求主体信息的临时文件名。

`$request_method` ：客户端请求的动作，通常为GET或POST。

`$remote_addr` ：客户端的IP地址。

`$remote_port` ：客户端的端口。

`$remote_user` ：已经经过Auth Basic Module验证的用户名。

`$request_filename` ：当前请求的文件路径，由root或alias指令与URI请求生成。

`$query_string` ：与$args相同。

`$scheme` ：HTTP方法（如http，https）。

`$server_protocol` ：请求使用的协议，通常是HTTP/1.0或HTTP/1.1。

`$server_addr` ：服务器地址，在完成一次系统调用后可以确定这个值。

`$server_name` ：服务器名称。

`$server_port` ：请求到达服务器的端口号。

`$request_uri ：包含请求参数的原始URI，不包含主机名，如` ：”/foo/bar.php?arg=baz”。

`$uri` ：不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。

`$document_uri` ：与$uri相同。

## 负载均衡

Nginx负载均衡状态

* `down`：当前的server暂时不参与负载均衡
* `backup`： 预留的备份服务器，当主服务器不可用的时候使用
* `max_fails`： 允许请求失败的次数
* `fail_timeout`： 经过max_fails次失败后，服务暂停的时间
* `max_conns`： 限制最大的接收连接数

使用示例

```config
http{
    upstream testserver {   
        server 192.168.1.5:8080 down;
        server 192.168.1.6:8080 backup;
        server 192.168.1.7:8080;
        server 192.168.1.8:8080 max_fails=3 fail_timeout=15;
    }

    server {
        location  / {
            proxy_pass  http://testserver;
        }
    }
}
```

Nginx负载均衡策略

* 轮询：默认方式
* `weight`： 权重方式，默认为1，权重数字越大，被分配到请求的几率越大。
* `ip_hash`： 依据ip分配方式
* `least_conn`： 依据最少连接方式
* `url_hash`： 依据URL分配方式
* `fair`： 依据响应时间方式

使用示例

```config
http{
    upstream testserver {
      # 以下选一种即可
      ip_hash; # ip hash 法
      least_conn; # 最少连接法
      hash $request_uri; # url hash 法
      server 192.168.1.5:8080 weight=3; # 权重法
      server 192.168.1.6:8080;
      server 192.168.1.7:8080;
  }
}
```

### HTTP

Http 的负载均衡属于 Nginx 七层负载均衡

```config
http {
    ……
    upstream testserver {   
      server 192.168.1.5:8080;
      server 192.168.1.6:8080;
      ……
    }

    server {
        ……
        location  / {
           ……     
           proxy_pass  http://testserver;
        } 
    }
}
```

### TCP

Tcp 的负载均衡属于 Nginx 四层负载均衡

示例

```config
stream {
    upstream mysql_read {
        server read1.example.com:3306 weight=5; 
        server read2.example.com:3306;
        server 10.10.12.34:3306 backup;
    }
    server {
        listen 3306;
        proxy_pass mysql_read;
    }
}
```

### UDP

Udp 的负载均衡属于 Nginx 四层负载均衡

示例

```config
stream {
    upstream ntp {
        server ntp1.example.com:123 weight=2; 
        server ntp2.example.com:123;
    }
    server {
        listen 123 udp; 
        proxy_pass ntp;
    }
}
```

## 解决跨域

转载自：<https://github.com/dunwu/nginx-tutorial>

首先，在 enable-cors.conf 文件中设置 cors ：

```nginx
# allow origin list
set $ACAO '*';

# set single origin
if ($http_origin ~* (www.helloworld.com)$) {
  set $ACAO $http_origin;
}

if ($cors = "trueget") {
 add_header 'Access-Control-Allow-Origin' "$http_origin";
 add_header 'Access-Control-Allow-Credentials' 'true';
 add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
 add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
}

if ($request_method = 'OPTIONS') {
  set $cors "${cors}options";
}

if ($request_method = 'GET') {
  set $cors "${cors}get";
}

if ($request_method = 'POST') {
  set $cors "${cors}post";
}
```

接下来，在你的服务器中 `include enable-cors.conf` 来引入跨域配置：

```nginx
# ----------------------------------------------------
# 此文件为项目 nginx 配置片段
# 可以直接在 nginx config 中 include（推荐）
# 或者 copy 到现有 nginx 中，自行配置
# www.helloworld.com 域名需配合 dns hosts 进行配置
# 其中，api 开启了 cors，需配合本目录下另一份配置文件
# ----------------------------------------------------
upstream front_server{
  server www.helloworld.com:9000;
}
upstream api_server{
  server www.helloworld.com:8080;
}

server {
  listen       80;
  server_name  www.helloworld.com;

  location ~ ^/api/ {
    include enable-cors.conf;
    proxy_pass http://api_server;
    rewrite "^/api/(.*)$" /$1 break;
  }

  location ~ ^/ {
    proxy_pass http://front_server;
  }
}
```

到此，就完成了。

## 搭建文件服务器

转载自：<https://github.com/dunwu/nginx-tutorial>

有时候，团队需要归档一些数据或资料，那么文件服务器必不可少。使用 Nginx 可以非常快速便捷的搭建一个简易的文件服务。

Nginx 中的配置要点：

* 将 autoindex 开启可以显示目录，默认不开启。
* 将 autoindex_exact_size 开启可以显示文件的大小。
* 将 autoindex_localtime 开启可以显示文件的修改时间。
* root 用来设置开放为文件服务的根路径。
* charset 设置为 `charset utf-8,gbk;`，可以避免中文乱码问题（windows 服务器下设置后，依然乱码，本人暂时没有找到解决方法）。

一个最简化的配置如下：

```nginx
autoindex on;# 显示目录
autoindex_exact_size on;# 显示文件大小
autoindex_localtime on;# 显示文件时间

server {
    charset      utf-8,gbk; # windows 服务器下设置后，依然乱码，暂时无解
    listen       9050 default_server;
    listen       [::]:9050 default_server;
    server_name  _;
    root         /share/fs;
}
```
