> ## windows本地前后端分离，项目部署+内网穿透

- ### 前端部署（vue，nginx）

   1. 修改配置文件的后端接口地址（你准备部署到外网的接口地址）
   2. 在终端运行：npm run build（此时会在项目里生成dist目录）
   3. 下载：[ng](http://nginx.org/en/download.html)
   4. 将dist目录里的内容复制到ng的html目录下
   5. 修改ng的conf目录下的nginx.conf
   6. 运行nginx.exe
   7. 前端部署成功

- ### 后端部署（springboot）

   1. 在终端运行：mvn clean package -T8（此时会在项目里生成target目录）

   2. 拿到target目录下的jar包

   3. 可以在jar包同级目录下创建个启动bat文件

   4. ```bash
      @echo off
      title 项目名称
      java -jar ./jar包名称.jar --server.port=8070 --spring.profiles.active=dev
      ```

   5. 后端部署成功

- ### 内网穿透（frp）

   1. 准备一台外网服务器（linux）

   2. 下载：[frp](https://github.com/fatedier/frp/releases/download/v0.44.0/frp_0.44.0_linux_amd64.tar.gz) （[中文文档](https://gofrp.org/docs/)）

   3. 首先配置外网的服务端frps.ini

   4. ```
      [common]
      bind_port = 7000 #服务端监听端口与客户端的server_port保持一致
      vhost_http_port = 8080 #HTTP 类型代理监听的端口
      token =123  #鉴权使用的 token 值 与frpc保持一致
      ```

   5. 可以在frps.ini同级目录下创建个启动bat文件

   6. ```
      @echo off
      ./frps -c frps.ini
      ```

   7. 想要调用bat文件,需要写绝对路径,比如"/home/myDir/xxx.bat",或者是切换到bat文件所在的目录,然后键入:"./xxx.bat".这里的"./"是告诉系统在当前目录下找名为"xxx.bat"的文件执行。

   8. 在执行bat文件之前,确保bat文件的权限是可执行的,如果没改权限的话,很有可能会报错误:Permission denied.更改权限的方式请自己查询"chmod"命令的使用方法.

   9. frps启动成功

   10. 内网windows客户端（前后端均部署成功）

   11. 下载：[frp](https://github.com/fatedier/frp/tags) （[中文文档](https://gofrp.org/docs/)）

   12. 配置内网客户端frpc.ini

   13. ```
       [common]
       server_addr = xxx #外网ip
       server_port = 7000 #与服务端的bind_port保持一致
       token =123 #鉴权使用的 token 值 与frps保持一致 token必须写在common下
       [nginx] #前端
       type = http
       local_ip = 127.0.0.1
       local_port = 8081
       custom_domains = xxx #买了域名则可以使用该域名，否则使用外网ip
       [api] #后端
       type = tcp
       local_ip = 127.0.0.1
       local_port = 8070
       remote_port = 8070
       ```

   14. 可以在frpc.ini同级目录下创建个启动bat文件

   15. ```bash
       @echo off
       title frpc
       frpc -c ./frpc.ini
       ```

       