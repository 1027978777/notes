> ### linux部署前端项目

- #### 安装[node](https://github.com/nvm-sh/nvm#installing-and-updating)

  推荐安装nvm来安装和管理node版本：

  ```
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
  wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
  #通过nvm安装nodejs
  nvm install node
  ```

  **注意：**

  在终端直接执行nvm没问题，执行shell脚本中的nvm提示bash: nvm: command not found…
  原因：nvm是一个脚本不是指令，所以shell脚本中执行nvm会提示bash: nvm: command not found…
  解决：只需在执行nvm前加一行指令即可解决问题：source ~/.nvm/nvm.sh
  注意： ~/.nvm是nvm的安装路径，需要写nvm的实际安装路径，可以用find / -name “.nvm” 来查找nvm的安装目录

- #### 安装[nginx](http://nginx.org/en/linux_packages.html)

  先创建/etc/yum.repos.d/nginx.repo文件内容如下：

  ```
  [nginx-stable]
  name=nginx stable repo
  baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
  gpgcheck=1
  enabled=1
  gpgkey=https://nginx.org/keys/nginx_signing.key
  module_hotfixes=true
  
  [nginx-mainline]
  name=nginx mainline repo
  baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
  gpgcheck=1
  enabled=0
  gpgkey=https://nginx.org/keys/nginx_signing.key
  module_hotfixes=true
  ```

  再运行：

  ```
  sudo yum install nginx
  ```

- ## 运行nginx

  ```
  whereis nginx
  ```

  ![image-20220820201326904](C:\Users\10279\AppData\Roaming\Typora\typora-user-images\image-20220820201326904.png)

  安装后网站的配置文件会在 **/etc/nginx/conf.d/**目录下，新增网站时只要在此目录下新增一份配置文件，或者直接应用/etc/nginx/nginx.conf文件：

  ```
  # For more information on configuration, see:
  #   * Official English Documentation: http://nginx.org/en/docs/
  #   * Official Russian Documentation: http://nginx.org/ru/docs/
  
  user nginx;
  worker_processes auto;
  error_log /var/log/nginx/error.log;
  pid /run/nginx.pid;
  
  # Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
  include /usr/share/nginx/modules/*.conf;
  
  events {
      worker_connections 1024;
  }
  
  http {
      log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';
  
      access_log  /var/log/nginx/access.log  main;
  
      sendfile            on;
      tcp_nopush          on;
      tcp_nodelay         on;
      keepalive_timeout   65;
      types_hash_max_size 4096;
  
      include             /etc/nginx/mime.types;
      default_type        application/octet-stream;
  
      # Load modular configuration files from the /etc/nginx/conf.d directory.
      # See http://nginx.org/en/docs/ngx_core_module.html#include
      # for more information.
      include /etc/nginx/conf.d/*.conf;
  
      server {
          listen       80;
          listen       [::]:80;
          server_name  _;
          root         /usr/share/nginx/html;
  
          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;
  
          error_page 404 /404.html;
          location = /404.html {
          }
  
          error_page 500 502 503 504 /50x.html;
          location = /50x.html {
          }
      }
  
  # Settings for a TLS enabled server.
  #
  #    server {
  #        listen       443 ssl http2;
  #        listen       [::]:443 ssl http2;
  #        server_name  _;
  #        root         /usr/share/nginx/html;
  #
  #        ssl_certificate "/etc/pki/nginx/server.crt";
  #        ssl_certificate_key "/etc/pki/nginx/private/server.key";
  #        ssl_session_cache shared:SSL:1m;
  #        ssl_session_timeout  10m;
  #        ssl_ciphers HIGH:!aNULL:!MD5;
  #        ssl_prefer_server_ciphers on;
  #
  #        # Load configuration files for the default server block.
  #        include /etc/nginx/default.d/*.conf;
  #
  #        error_page 404 /404.html;
  #            location = /40x.html {
  #        }
  #
  #        error_page 500 502 503 504 /50x.html;
  #            location = /50x.html {
  #        }
  #    }
  
  }
  ```

  可以看到 root         /usr/share/nginx/html；我们此时只需要将前端项目打包，将dist目录下的内容复制到 /usr/share/nginx/html目录下，再输入：

  ```
  /usr/sbin/nginx -c /etc/nginx/nginx.conf
  ```

  然后重新应用下配置文件就可以了。这里介绍下nginx两个常用的命令：

  ```
  #测试配置文件是否正常
  nginx -t
  #重新应用配置文件
  nginx -s reload
  ```

  







