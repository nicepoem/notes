# Nginx

## 安装

1、更新软件包列表，升级已安装的软件包。

```bash
sudo apt update
sudo apt upgrade
```

2、安装 Nginx

```bash
sudo apt install -y nginx
```

3、查看 Nginx 版本

```bash
nginx -v
```

4、查看 Nginx 状态

```bash
sudo systemctl status nginx
```

运行成功：

```bash{2}
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Mon 2026-06-08 19:08:33 CST; 16min ago
 Invocation: dfd185e21fb546e7a268a78316becc37
       Docs: man:nginx(8)
    Process: 1384 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, sta>
    Process: 1394 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUC>
   Main PID: 1403 (nginx)
      Tasks: 3 (limit: 6092)
     Memory: 7.2M (peak: 8.2M)
        CPU: 52ms
     CGroup: /system.slice/nginx.service
             ├─1403 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             ├─1404 "nginx: worker process"
             └─1405 "nginx: worker process"
```

## 管理 Nginx 服务

使用 `systemctl` 命令来控制Nginx服务的生命周期。

```bash
# 启动 Nginx
sudo systemctl start nginx

# 停止 Nginx
sudo systemctl stop nginx

# 重启 Nginx
sudo systemctl restart nginx

# 重载配置（不中断服务）
sudo systemctl reload nginx

# 查看状态
sudo systemctl status nginx

# 设置开机自启
sudo systemctl enable nginx

# 禁止开机自启
sudo systemctl disable nginx

# 测试配置文件语法是否正确
sudo nginx -t

# 查看错误日志
sudo tail -f /var/log/nginx/error.log
```


监听端口

```bash
ss -lntp
```

查看端口

```bash{4,8}
State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process     
LISTEN      0           4096             127.0.0.53%lo:53                  0.0.0.0:*                    
LISTEN      0           4096                127.0.0.54:53                  0.0.0.0:*                    
LISTEN      0           511                    0.0.0.0:80                  0.0.0.0:*        # IPv4            
LISTEN      0           4096                   0.0.0.0:22                  0.0.0.0:*                    
LISTEN      0           128                  127.0.0.1:6010                0.0.0.0:*                    
LISTEN      0           4096                 127.0.0.1:631                 0.0.0.0:*                    
LISTEN      0           511                       [::]:80                     [::]:*        # IPv6            
LISTEN      0           4096                      [::]:22                     [::]:*                    
LISTEN      0           4096                     [::1]:631                    [::]:*                    
LISTEN      0           128                      [::1]:6010                   [::]:*   
```

## 配置

Nginx 目录结构树状图

```bash{2,5,6,8}
/etc/nginx/
├── nginx.conf                    # 主配置文件 - Nginx 核心配置入口
├── mime.types                    # MIME 类型映射 - 定义文件扩展名与 Content-Type 对应关系
│
├── sites-available/              # 站点可用配置目录 - 存放所有网站的虚拟主机配置（未激活）
├── sites-enabled/                # 站点已启用目录 - 存放指向 sites-available/ 的软链接（激活的站点）
│
├── conf.d/                       # 额外配置目录 - 存放独立的配置文件（自动加载 *.conf）
├── snippets/                     # 配置片段目录 - 存放可复用的配置片段
│
├── modules-available/            # 模块可用目录 - 存放可加载的动态模块配置（.conf 文件）
├── modules-enabled/              # 模块已启用目录 - 存放指向 modules-available/ 的软链接（激活的模块）
│
├── fastcgi.conf                  # FastCGI 完整配置 - 包含所有 FastCGI 参数（用于 PHP-FPM 等）
├── fastcgi_params                # FastCGI 基础参数 - 被 fastcgi.conf 引用，定义基础参数
├── scgi_params                   # SCGI 参数配置 - 用于 SCGI 协议（较少使用）
├── uwsgi_params                  # uWSGI 参数配置 - 用于 Python uWSGI 应用（如 Django）
├── proxy_params                  # 反向代理参数 - 包含标准代理头设置（X-Real-IP, X-Forwarded-For 等）
│
├── koi-utf                       # KOI8-R 到 UTF-8 编码转换表（俄语相关）
├── koi-win                       # KOI8-R 到 Windows-1251 编码转换表（俄语相关）
└── win-utf                       # Windows-1251 到 UTF-8 编码转换表（俄语相关）
```

- nginx.conf

```bash
# 运行 Nginx 工作进程的系统用户
# 使用低权限用户 www-data 提升安全性，防止 Web 漏洞导致系统被入侵
user www-data;

# 工作进程数量
# auto 表示自动检测 CPU 核心数并创建相同数量的进程，充分利用多核 CPU
worker_processes auto;

# CPU 亲和性
# auto 表示自动将每个工作进程绑定到独立的 CPU 核心，减少进程切换开销，提升性能
worker_cpu_affinity auto;

# 主进程 PID 文件路径
# 用于 systemd 等服务管理工具跟踪和控制 Nginx 进程
pid /run/nginx.pid;

# 错误日志配置
# 记录 Nginx 运行过程中的错误、警告和调试信息，是排查问题的关键文件
# 级别可选：debug, info, notice, warn, error, crit, alert, emerg
error_log /var/log/nginx/error.log;

# 加载动态模块
# 通过 apt install nginx-module-* 安装的模块会在此目录生成配置文件
include /etc/nginx/modules-enabled/*.conf;

# events 块：配置网络连接相关参数
events {
    # 每个工作进程能够同时打开的最大连接数
    # 总最大连接数 = worker_connections × worker_processes
    # 默认值 768 适合大多数场景，高并发时可调大（如 1024 或 4096）
    worker_connections 768;
    
    # 是否一次性接受所有新连接
    # 注释掉表示 off（默认），即每次只接受一个连接
    # 开启后高流量下可能提升性能，但也会增加瞬时负载
    # multi_accept on;
}

# http 块：Web 服务的核心配置
http {
    ##
    # 基础设置
    ##
    
    # 启用 sendfile 系统调用
    # 数据在磁盘和网络套接字之间直接传输，绕过用户空间拷贝
    # 可显著提高静态文件传输效率（尤其是大文件）
    sendfile on;
    
    # 与 sendfile on 配合使用
    # 开启后会在发送响应头和数据包时合并多个数据包，减少网络往返次数
    # 提高网络利用率，特别适合大文件传输
    tcp_nopush on;
    
    # MIME 类型哈希表最大大小
    # 当配置了大量自定义 MIME 类型时，可能需要增大此值防止哈希冲突
    types_hash_max_size 2048;
    
    # 是否在错误页面和响应头中显示 Nginx 版本信息
    # build 会显示完整版本号和构建信息（如 "nginx/1.18.0 (Ubuntu) build"）
    # 推荐改为 off，只显示 "nginx"，防止攻击者利用特定版本漏洞
    # 可选值：on | off | build
    server_tokens build;
    
    # 服务器名称哈希表桶大小（被注释，使用默认值 32 或 64）
    # 如果配置了很多长域名（如超过 64 字节），可能需要取消注释并增大此值
    # server_names_hash_bucket_size 64;
    
    # 重定向时是否使用 server_name 指定的主机名（被注释，默认 off）
    # off 表示使用请求头中的 Host 字段，通常更符合预期
    # server_name_in_redirect off;
    
    # MIME 类型映射文件
    # 定义文件扩展名与 Content-Type 的对应关系（如 .html → text/html）
    include /etc/nginx/mime.types;
    
    # 默认 MIME 类型
    # 当无法匹配文件扩展名时使用，application/octet-stream 会强制浏览器下载文件
    default_type application/octet-stream;
    
    ##
    # SSL/TLS 安全设置
    ##
    
    # 允许的 TLS 协议版本
    # 只启用 TLSv1.2 和 TLSv1.3，弃用不安全的 SSLv3、TLSv1.0、TLSv1.1
    # 这是现代安全标准，兼顾兼容性和安全性
    ssl_protocols TLSv1.2 TLSv1.3;
    
    # 是否优先使用服务器端定义的加密套件顺序
    # off 表示让客户端选择加密套件（现代浏览器通常有更优的选择）
    # on 表示强制使用服务器端的顺序（旧版兼容场景）
    ssl_prefer_server_ciphers off;
    
    ##
    # 日志设置
    ##
    
    # 访问日志路径
    # 记录每个客户端请求的详细信息（IP、请求方法、状态码、响应大小、耗时等）
    # 可用于分析流量、排查问题、检测攻击
    access_log /var/log/nginx/access.log;
    
    ##
    # Gzip 压缩设置
    ##
    
    # 启用 Gzip 压缩
    # 对响应内容进行压缩，减少传输数据量，提升页面加载速度
    gzip on;
    
    # 以下是被注释的高级 Gzip 设置，建议按需开启
    
    # 添加 Vary: Accept-Encoding 响应头
    # 告诉代理服务器根据客户端是否支持压缩来缓存不同版本
    # gzip_vary on;
    
    # 是否对代理请求的响应进行压缩
    # any 表示压缩所有代理请求的响应
    # gzip_proxied any;
    
    # 压缩级别（1-9）
    # 数值越大压缩率越高但消耗 CPU 越多，6 是性能和压缩率的平衡点
    # gzip_comp_level 6;
    
    # 压缩缓冲区大小和数量
    # 16 个 8k 的缓冲区，用于暂存压缩数据
    # gzip_buffers 16 8k;
    
    # 启用压缩的最低 HTTP 版本
    # 1.1 及以上版本支持压缩，1.0 客户端可能不支持
    # gzip_http_version 1.1;
    
    # 需要压缩的 MIME 类型
    # 只压缩文本类资源，图片/视频等已压缩格式不再重复压缩
    # gzip_types text/plain text/css application/json application/javascript 
    #            text/xml application/xml application/xml+rss text/javascript;
    
    ##
    # 虚拟主机配置引入
    ##
    
    # 引入 conf.d 目录下的所有配置文件（通用配置片段）
    include /etc/nginx/conf.d/*.conf;
    
    # 引入 sites-enabled 目录下的所有配置文件（已启用的站点配置）
    # 这是 Ubuntu/Debian 系的标准做法，sites-available 存放可用配置，
    # sites-enabled 通过符号链接存放已激活的配置，便于管理
    include /etc/nginx/sites-enabled/*;
}

# mail 块：邮件代理服务配置（本例中被注释，表示未启用）
# 如果需要 Nginx 作为 IMAP/POP3 邮件代理服务器，可取消注释并配置
#mail {
#    # 认证脚本示例
#    # auth_http localhost/auth.php;
#    
#    # 声明支持的 POP3 能力
#    # pop3_capabilities "TOP" "USER";
#    
#    # 声明支持的 IMAP 能力
#    # imap_capabilities "IMAP4rev1" "UIDPLUS";
#    
#    # POP3 代理服务器配置
#    # server {
#    #     listen     localhost:110;
#    #     protocol   pop3;
#    #     proxy      on;
#    # }
#    
#    # IMAP 代理服务器配置
#    # server {
#    #     listen     localhost:143;
#    #     protocol   imap;
#    #     proxy      on;
#    # }
#}
```

| 目录                          | 用途                         |
| :---------------------------- | :--------------------------- |
| `/etc/nginx/nginx.conf`       | 主配置文件                   |
| `/etc/nginx/sites-available/` | 所有站点的配置文件（仓库）   |
| `/etc/nginx/sites-enabled/`   | 已启用的站点配置（符号链接） |
| `/etc/nginx/conf.d/`          | 通用配置片段                 |
| `/var/www/html/`              | 默认网站根目录               |
| `/var/log/nginx/`             | 日志文件目录                 |

```bash
##
# 建议阅读的文档链接（帮助你深入理解 Nginx 配置）：
# https://www.nginx.com/resources/wiki/start/                           - Nginx 官方入门指南
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/  - 常见配置陷阱
# https://wiki.debian.org/Nginx/DirectoryStructure                     - Debian/Ubuntu 的 Nginx 目录结构说明
#
# 通常情况下，管理员会从 sites-enabled/ 目录中删除此文件，
# 将其保留在 sites-available/ 中作为参考。该文件会由 Nginx 打包团队持续更新。
#
# 此文件会自动加载其他应用程序（如 Drupal 或 Wordpress）提供的配置文件。
# 这些应用程序可以通过包名对应的路径访问，例如 /drupal8。
#
# 更多详细示例请参考：/usr/share/doc/nginx-doc/examples/
##

# 默认服务器配置
#
server {
    # 监听 IPv4 的 80 端口，并设置为默认服务器
    # default_server 表示当请求的 Host 头没有匹配到任何 server_name 时，由此 server 块处理
    listen 80 default_server;
    
    # 监听 IPv6 的 80 端口，同样设置为默认服务器
    # [:::] 表示所有 IPv6 地址
    listen [::]:80 default_server;

    # ============================================
    # SSL 配置（默认被注释，HTTPS 相关）
    # ============================================
    #
    # 监听 443 端口（HTTPS 默认端口），启用 SSL，并设为默认服务器
    # listen 443 ssl default_server;
    # listen [::]:443 ssl default_server;
    #
    # 注意：对于 SSL 流量，你应该禁用 Gzip 压缩
    # 原因：Gzip 压缩可能引发 BREACH 攻击，且 SSL 已经加密，再压缩收益不大
    # 参考：https://bugs.debian.org/773332
    #
    # 阅读 ssl_ciphers 相关文档，确保配置安全
    # 参考：https://bugs.debian.org/765782
    #
    # ssl-cert 包生成的自签名证书
    # 注意：不要在生产环境中使用自签名证书！
    # include snippets/snakeoil.conf;

    # ============================================
    # 网站根目录设置
    # ============================================
    # 网站文件的存放路径
    # 当请求到达时，Nginx 会从这个目录下寻找对应的文件
    root /var/www/html;

    # 默认首页文件列表（按顺序查找）
    # 当访问目录时（如 http://example.com/），会依次查找这些文件并返回第一个存在的
    # index.nginx-debian.html 是 Ubuntu/Debian 默认安装时生成的欢迎页
    index index.html index.htm index.nginx-debian.html;

    # 服务器名称
    # 下划线 "_" 是一个通配符，匹配所有未明确指定的域名
    # 这就是为什么这个 server 块会处理所有请求
    server_name _;

    # ============================================
    # 位置匹配块 - 根路径 "/" 的处理规则
    # ============================================
    location / {
        # try_files 指令：按顺序尝试提供文件/目录，都找不到则返回 404
        # $uri          - 请求的 URI 路径（如 /about.html）
        # $uri/         - 将请求视为目录，尝试查找目录下的 index 文件
        # =404          - 以上都失败时，返回 HTTP 404 错误
        try_files $uri $uri/ =404;
    }

    # ============================================
    # PHP 脚本处理（默认被注释）
    # ============================================
    # 将以 .php 结尾的请求转发到 FastCGI 服务器（如 PHP-FPM）
    #
    #location ~ \.php$ {
    #    # 引入 FastCGI 通用配置片段（包含常见参数设置）
    #    include snippets/fastcgi-php.conf;
    #
    #    # 使用 Unix Socket 方式连接 PHP-FPM（推荐，性能更好）
    #    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    #
    #    # 或者使用 TCP Socket 方式连接 PHP-FPM
    #    # fastcgi_pass 127.0.0.1:9000;
    #}

    # ============================================
    # 禁止访问 .htaccess 等隐藏文件
    # ============================================
    # Apache 使用 .htaccess 进行目录级配置，Nginx 不支持这种方式
    # 为了安全，直接拒绝所有以 /.ht 开头的请求
    #
    #location ~ /\.ht {
    #    deny all;    # 返回 403 禁止访问
    #}
}


# ============================================
# 虚拟主机配置示例（以 example.com 为例）
# ============================================
#
# 你可以将以下配置移到 sites-available/ 下的独立文件中，
# 并在 sites-enabled/ 中创建符号链接来启用它。
#
#server {
#    # 监听 IPv4 的 80 端口
#    listen 80;
#    # 监听 IPv6 的 80 端口
#    listen [::]:80;
#
#    # 服务器域名（多个域名用空格分隔）
#    server_name example.com;
#
#    # 网站根目录（每个站点可以有自己的根目录）
#    root /var/www/example.com;
#    
#    # 默认首页文件
#    index index.html;
#
#    # 根路径的处理规则
#    location / {
#        try_files $uri $uri/ =404;
#    }
#}
```

### 4. 启用新站点的标准流程

```bash
# 1. 在 sites-available 中创建配置文件
sudo nano /etc/nginx/sites-available/mywebsite

# 2. 创建符号链接到 sites-enabled
sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/

# 3. 测试配置语法
sudo nginx -t

# 4. 重载配置
sudo systemctl reload nginx
```
