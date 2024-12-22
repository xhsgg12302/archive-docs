---
weight: 40
---

* ## 简介

    + ### 1.介绍

		> [?] Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务,支持FastCGI,SSL,Virtual Host,URL Rewrite,Gzip等功能。

	+ ### 2.功能

		* 反向代理
		* 负载均衡
		* 静态服务器
		* 邮箱服务器

	+ ### 负载均衡方式

		|name | explain | feature|
		|-    | - | - |  
		| 轮询  	| 默认方式 		| |
		| weight 	| 权重方式		| 根据权重分发请求,权重大的分配到请求的概率大 |
		| ip_hash	| ip 分配 		| 根据客户端请求的IP地址计算hash值， 根据hash值来分发请求, 同一个IP发起的请求, 会发转发到同一个服务器上 |
		| url_hash	| url 分配 		| 根据客户端请求url的hash值，来分发请求, 同一个url请求, 会发转发到同一个服务器上 |
		| fair		| 智能响应		| 优先把请求分发给处理请求时间短的服务器 |
		| least_conn| 最少连接		| 哪个服务器当前处理的连接少, 请求优先转发到这台服务器 |

* ## 编译

	```bash
	cd nginx-1.16.1/
	./configure --prefix=/usr/local/nginx/ --with-http_gzip_static_module --with-http_ssl_module --with-http_v2_module --without-http_fastcgi_module
	make
	objs/nginx -V
	objs/nginx -h
	objs/nginx -c /usr/local/nginx/conf/nginx.conf -t
	# make install
	```

* ## 配置文件

	```nginx [data-file:nginx.conf,data-cc:400px]
	#=======================================
	# EXAMPLE and officia documents at (http://nginx.org/en/docs/)
	#=======================================
	#error_log  logs/error.log;
	#error_log  logs/error.log  notice;
	#error_log  logs/error.log  info;
	#access_log logs/brian.log main gzip buffer=128k flush=5s;
	#pid        logs/nginx.pid;

	# nginx compile
	# ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx/ --with-http_gzip_static_module --with-http_ssl_module --with-http_v2_module

	# 1.=======================================
	# 在nginx中配置proxy_pass时，如果是按照^~匹配路径时,要注意proxy_pass后的url最后的/
	# 当加上了/，相当于是绝对根路径，则nginx不会把location中匹配的路径部分代理走;
	# 如果没有/，则会把匹配的路径部分也给代理走.
	#
	# 2.=======================================
	# 代理重定向，如果关闭的话，重定向请求会直接映射到 http://tomcat/redirect/path
	# 开启后，重定向会通过nginx代理，项目内的重定向正常访问
	# proxy_redirect off;
	#
	# 3.=======================================
	# location /i/ {
	#	root /data/w3;
	# }
	# The /data/w3/i/top.gif file will be sent in response to the “/i/top.gif” request.
	#
	# location /i/ {
	#	alias /data/w3/images/;
	# }
	# on request of “/i/top.gif”, the file /data/w3/images/top.gif will be sent.
	#
	# 4.=======================================
	# HTTP Strict Transport Security(HSTS) and NGINX (https://www.nginx.com/blog/http-strict-transport-security-hsts-and-nginx/)
	# 307 from temporary redirect to Internal Redirect configuration
	# add_header Strict-Transport-Security "max-age=172800; includeSubDomains" always;
	# (add_header) directive [There’s one important exception] 有一个例外，当且仅当自身level没有add_header 指令时，才会继承上一层的。不然得在当前块重新声明。
	# There could be several add_header directives. These directives are inherited from the previous configuration level if and only if there are no add_header directives defined on the current level.
	# If the always parameter is specified (1.7.5), the header field will be added regardless of the response code.
	#
	# 5.=======================================
	# location = /uri		精确匹配
	# location ^~ /uri 		开头匹配，可以理解为项目名，一旦匹配，停止其他（正则匹配）
	# location ~ /uri		区分大小写的正则匹配
	# location ~* /uri		不分区大小写的正则匹配
	# location /uri			不带修饰符的字符匹配，在正则匹配之后
	# location /			通用匹配，相当于else 中的内容，<==> switch 中的default
	# example1:
	# location = / {
	#     [ configuration A ]
	# }
	#
	# location / {
	#     [ configuration B ]
	# }
	#
	# location /documents/ {
	#     [ configuration C ]
	# }
	#
	# location ^~ /images/ {
	#     [ configuration D ]
	# }
	#
	# location ~* \.(gif|jpg|jpeg)$ {
	#     [ configuration E ]
	# }
	#
	# The “/” request will match configuration A,
	# the “/index.html” request will match configuration B,
	# the “/documents/document.html” request will match configuration C,
	# the “/images/1.gif” request will match configuration D,
	# and the “/documents/1.jpg” request will match configuration E.
	#
	# example2:
	# location ^~ /helloworld { #1
	#    	 return 601;
	# }
	# #location /helloworld { #2
	# #    	 return 602;
	# #}
	# location ~ /helloworld {
	# 		 return 603;
	# }
	# http://localhost/helloworld/test，返回601。如将#1注释，#2打开，浏览器输入http://localhost/helloworld/test，返回603。
	# 注：#1和#2不能同时打开，如同时打开，启动nginx会报nginx: [emerg] duplicate location “/helloworld”…，因为这两个都是普通字符串。
	# location /helloworld/test/ {
	#		return 601;
	# }
	# location /helloworld/ {
	#		return 602;
	# }
	# http://localhost/helloworld/test/a.html，返回601。http://localhost/helloworld/a.html，返回602
	# location ~ /helloworld {
	#		return 602;
	# }
	# location ~ /helloworld/test {
	#		return 603;
	# }
	# http://localhost/helloworld/test/a.html，返回602；将#2和#3调换顺序，http://localhost/helloworld/test/a.html，返回603
	#
	# 6.=======================================
	# include_ico:
	# location /favicon.ico {
	# 		root html;
	#	 	access_log off;
	#     	try_files $uri /;
	#     	expires 10d;
	# }
	#
	# 7.=======================================
	# include_robots:
	# location =/robots.txt {
	#      	default_type text/html;
	#      	add_header Content-Type "text/plain; charset=UTF-8";
	#      	return 200 "User-Agent: *\nDisallow: /";
	# }
	#
	# TODO 1,nginx configure robot.txt and favicon.ico
	# TODO 2,rewrite operation
	# TODO 3,hard link edit line
	#
	#=======================================

	#user  nobody;
	worker_processes  1;

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
		keepalive_timeout  0;
		# keepalive_timeout  65;
		# 开启gzip
		gzip  on;
		# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
		gzip_min_length 1k;
		# gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间。一般设置1和2
		gzip_comp_level 2;
		# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
		gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png image/jpg;
		# 是否在http header中添加Vary: Accept-Encoding，建议开启
		gzip_vary on;
		# 禁用IE 6 gzip
		gzip_disable "MSIE [1-6]\.";
		# 设置缓存路径并且使用一块最大100M的共享内存，用于硬盘上的文件索引，包括文件名和请求次数，每个文件在1天内若不活跃（无请求）则从硬盘上淘汰，硬盘缓存最大10G，满了则根据LRU算法自动清除缓存。
		proxy_cache_path /var/cache/nginx/cache levels=1:2 keys_zone=imgcache:100m inactive=1d max_size=10g;
		# 用于tomcat hot deploy (upload war)
		client_max_body_size 200M;
		# haha
		# proxy_intercept_errors on;
		# fastcgi_intercept_errors on;
		# error_page 404 =200 /404.html;
		#=======================================
		# 隐藏返回头信息中nginx的版本号
		server_tokens off;
		# 隐藏server信息
		# 需要重新编译nginx
		# 进入解压出来的nginx源码目录
		# vim src/http/ngx_http_header_filter_module.c # 49-50
		# 修改以下两个值,后重新编译 example:Server: X-Web
		# static char ngx_http_server_string[] = "Server:nginx";
		# static cahr ngx_http_server_full_string[] = "Server:" NGINX_VER ;

		upstream tomcat{
			server 127.0.0.1:13380;
		}

		upstream activemq{
			server 127.0.0.1:8161;
		}

	#=======================================
		server {
			listen       80;
			server_name  example.com;

			return 307 https://example.com/;
			rewrite ^/(.*) https://example.com redirect;
			# include ../html/include_robots;
			# include ../html/include_ico;
			#
			# location / {
			#     root   html;
			#     index  index.html index.htm;
			# }

			# error_page 404 =200 /404.html;
			# error_page   500 502 503 504  /50x.html;
			# location = /50x.html {
			#     root   html;
			# }
		}

		#=======================================
		server {
			listen       80;
			server_name  test.example.com;

			location = / {
				proxy_set_header Host $host;
				proxy_set_header X-Real-IP $remote_addr;
				proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
				proxy_pass http://tomcat;
				proxy_redirect off;
				index index.html index.htm;
				proxy_intercept_errors on;
			}

			# include ../html/include_robots;
			# include ../html/include_ico;

			error_page 404 =200  @notfound;
			location @notfound {
				default_type text/html;
				# add_header Content-Type "text/plain; charset=UTF-8";
				return 200 "<html><head><title>4004 Not Found</title></head><body><center><h1>4004 Not Found</h1></center><hr><center>nginx</center></body></html>";
			}

			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
		}

		#=======================================
		server {
			listen       80;
			server_name  mvn.example.com;

			location / {
				root   html;
				index  index.html index.htm;
			}

			location ^~ /nexus/ {
				proxy_set_header Host $host;
				proxy_set_header X-Real-IP $remote_addr;
				proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
				proxy_pass http://127.0.0.1:10008;
			}

			# include ../html/include_robots;
			# include ../html/include_ico;

			error_page 404 =200 /404.html;
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
		}

		#=======================================
		server {
			listen       80;
			server_name  service.example.com;

			location = /speed {
				proxy_pass http://127.0.0.1:13330/speed/gui.html;
				proxy_redirect off;
			}

			location / {
				proxy_pass http://127.0.0.1:13330;
				proxy_http_version 1.1;
				proxy_read_timeout 360s;
				proxy_redirect off;
				proxy_set_header Upgrade $http_upgrade;
				proxy_set_header Connection "upgrade";
				proxy_set_header Host $host:$server_port;
				proxy_set_header X-Real-IP $remote_addr;
				proxy_set_header REMOTE-HOST $remote_addr;
				proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			}

			# include ../html/include_robots;
			# include ../html/include_ico;

			error_page 404 =200  @notfound;
			location @notfound {
				default_type text/html;
				# add_header Content-Type "text/plain; charset=UTF-8";
				return 200 "<html><head><title>4004 Not Found</title></head><body><center><h1>4004 Not Found</h1></center><hr><center>nginx</center></body></html>";
			}

			proxy_intercept_errors on;
			error_page 404 =200 /404.html;
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
		}

		# HTTPS server

		#=======================================
		server {
			listen 443 ssl;
			server_name www.example.com;
			ssl_certificate 3_www.wtfu.site_bundle.crt;
			ssl_certificate_key 3_www.wtfu.site.key;
			ssl_session_timeout 5m;
			ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
			ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
			ssl_prefer_server_ciphers on;
			# HSTS
			# add_header Strict-Transport-Security "max-age=172800; includeSubDomains" always;

			location / {
				# HSTS
				add_header Strict-Transport-Security "max-age=15552000" always; # includeSubDomains" always;
				root html;
				index index.html index.htm;
			}

			location = /_service_ {
				default_type 'text/html; charset=UTF-8';
				alias html/_service_.html;
				auth_basic		'please input u&s';
				auth_basic_user_file htpasswd;
			}

			location = /docker.html {
				# add_header Content-Type "application/pdf";
				add_header Content-Disposition 'inline; filename="docker.html"';
				# add_header Cache-Control "no-cache";
				keepalive_timeout 0;
				root html;
			}

			location ^~ /blog {
				alias html/docs/;
				index index.html;
				error_page 404  @notfounds;
				gzip_comp_level 8;
			}

			location ^~ /resume/ {
				# add_header Content-Type "application/pdf";
				add_header Content-Disposition 'inline; filename="wtfu.pdf"';
				# add_header Cache-Control "no-cache";
				keepalive_timeout 0;
				root html;
			}

			# include ../html/include_robots;
			# include ../html/include_ico;

			error_page 404 =200  @notfounds;
			location @notfounds {
				default_type text/html;
				# add_header Content-Type "text/plain; charset=UTF-8";
				return 200 "<html><head><title>4004 Not Found</title></head><body><center><h1>4004s Not Found</h1></center><hr><center>nginx</center></body></html>";
			}

			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
			# 开启缓存，关闭静态资源日志记录，节省服务器资源
			# location ~* ^.+\.(css|js|ico|gif|jpg|jpeg|png)$ {
				# log_not_found off;
				# 关闭日志
				# access_log off;
				# 缓存时间7天
				# expires 7d;
				# 源服务器
				# proxy_pass http://tomcat;
				# 指定上面设置的缓存区域
				# proxy_cache imgcache;
				# 缓存过期管理
				# proxy_cache_valid 200 302 1d;
				# proxy_cache_valid 404 10m;
				# proxy_cache_valid any 1h;
				# proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
			# }
		}
	}
	```

* ## 应用

	+ ### 1.使用http smart 协议搭建git 服务器

		- #### 安装fastcgi(fcgiwrap)

			* mac `brew install fcgiwrap`
			* 其他源码安装（https://github.com/gnosek/fcgiwrap.git）

				> centos提示
				checking for FCGX_Init in -lfcgi... no
				configure: error: FastCGI library is missing
				<br><br>需要安装 fcgi-devel :https://centos.pkgs.org/7/epel-x86_64/fcgi-devel-2.4.0-25.el7.x86_64.rpm.html

			* 启动fcgiwrap

				> `./fcgiwrap -s help` 查看-s选项
				> 例 `./fcgiwrap -s tcp:127.0.0.1:12302`
				> 或 `./fcgiwrap -s unix:/tmp/cgi.sock`

		- #### nginx配置

			> 默认包含fastcgi模块，只需要简单指令

			```nginx [data-cc:400px]
			location /test/ {

				#include /usr/local/etc/nginx/fastcgi_params;
				# alias html; # @DOCUMENT_ROOT;

				fastcgi_pass 127.0.0.1:12302; # fcgiwrap -s tcp:127.0.0.1:12302
				fastcgi_connect_timeout 24h;
				fastcgi_read_timeout 24h;
				fastcgi_send_timeout 24h;
				#fastcgi_param SCRIPT_FILENAME /usr/local/var/www/@fastcgi_script_name;
				include fastcgi_params;

				fastcgi_param SCRIPT_FILENAME /Users/stevenobelia/Desktop/gittest/abc.cgi;
				fastcgi_param PATH_INFO $uri;
			}
			location ^~ /project {
				auth_basic "Git Login";
				auth_basic_user_file "/usr/local/etc/nginx/pass.db";

				include fastcgi_params;

				fastcgi_connect_timeout 24h;
				fastcgi_read_timeout 24h;
				fastcgi_send_timeout 24h;
				fastcgi_pass	unix:/tmp/cgi.sock;
				fastcgi_param CONTENT_LENGTH  $content_length;

				fastcgi_param REQUEST_METHOD  $request_method;
				fastcgi_param SCRIPT_FILENAME /Library/Developer/CommandLineTools/usr/libexec/git-core/git-http-backend;
				fastcgi_param GIT_PROJECT_ROOT /Users/stevenobelia/Documents/_anft-backup/idea-project;
				fastcgi_param PATH_INFO $uri;
				fastcgi_param REMOTE_USER $remote_user;
				fastcgi_param GIT_HTTP_EXPORT_ALL "";
				#alias /Users/stevenobelia/Documents/_haohuo-backup/haohuo-data/project;
				#autoindex on; #自动索引
				#autoindex_exact_size off; #使得文件大小以MB,GB形式显示而非KB
				#autoindex_localtime on; #使用本地时间
				#index index.html index.htm;
			}
			```
			
		- #### script-file[abc.cgi]

			```bash
			#!/bin/bash

			echo -e "Content-Type: text/html\n"
			echo "<html><head>"
			echo "</head><body>"
			echo "helloworld"
			echo "</body></html>"
			```

		- ##### reference

			* https://github.com/gnosek/fcgiwrap
			* https://centos.pkgs.org/7/epel-x86_64/fcgi-devel-2.4.0-25.el7.x86_64.rpm.html
			* https://www.nginx.com/resources/wiki/start/topics/examples/fcgiwrap/
			* https://techexpert.tips/nginx/nginx-shell-script-cgi/
			* https://www.howtoforge.com/serving-cgi-scripts-with-nginx-on-centos-6.0-p2
			* https://sleeplessbeastie.eu/2017/09/18/how-to-execute-cgi-scripts-using-fcgiwrap/


* ## 工具

	+ ### 1.使用 acme.sh 自动化管理 ssl/tsl证书

		```shell {18,22,27,29}
		# 工具安装 acme.sh (https://github.com/acmesh-official/acme.sh)
		curl https://get.acme.sh | sh -s email=xhsgg12302@126.com

		# 工具升级
		acme.sh --upgrade
		# 工具保持自动升级
		acme.sh --upgrade --auto-upgrade

		# 工具配置
		# 颁发证书，使用 dnsapi 进行验证
		# 此处使用的是腾讯统一后的api 3.0[腾讯云 API 密钥]
		# 如果为dnspod的话，可以修改 [--dns dns_dp]
		# 需要注意的是如果使用的是 上面的dnsapi的话，需要DNSPod控制台(https://console.dnspod.cn/account/token/token)创建的token[DNSPod Token]而不是[腾讯云 API 密钥]。
		# 且导入的环境变量不通，参考(https://github.com/acmesh-official/acme.sh/wiki/dnsapi#2-dnspodcn-option)
		export Tencent_SecretId="xxxxx"
		export Tencent_SecretKey="xxxxx"

		# 证书 颁发
			# 通配符域名可以和主域名共存。生成一张证书，多个域名。
			acme.sh --issue --dns dns_tencent  -d wtfu.site -d *.wtfu.site

		# 证书 安装 
		# 建议不要手动复制，使用install-cert 命令安装后会记录相关参数在域名文件下的conf文件中。
		# 所以后续的操作，比如renew，cron等会使用到相关参数，以达到自动续期的功能。cron的话一般无需人工干预。且为 dnsapi模式。
			acme.sh --install-cert -d wtfu.site --fullchain-file /usr/local/nginx/conf/certificate/multi/full_chain.pem  --key-file /usr/local/nginx/conf/certificate/multi/private.key --reloadcmd "/usr/local/nginx/sbin/nginx -s reload"

		# 证书 更新 （手动单个）
			acme.sh --renew -d example.com [--force]
		# 证书 更新 （批量）
			acme.sh --cron [--force]
		```

		+ Reference

			* https://blog.freessl.cn/acme-quick-start/