---
layout: post

title: 使用Nignx在Linux上部署Asp.Net Core网站

description: 使用Nignx在Linux上部署Asp.Net Core网站

category: programming

tags: [netcore, nginx]
---

## 准备工作

1. ASP.NET Core应用程序
1. Linux服务器，这里以Ubuntu为例，登录账户需要具有sudo或root权限

## Ubuntu设置

以示例域名www.test.zjf为例，需要在/etc/hosts中添加如下记录：

```hosts
127.0.0.1 www.test.zjf
```

假设Linux服务器的IP为：192.168.1.201，访问网站的机器也需要添加一条hosts记录：

```hosts
192.168.1.201 www.test.zjf
```

### [安装dotnet core](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x)，以Ubuntu1604为例

1. 注册微软产品Key
    ```sh
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```
1. 设置软件源
    ```sh
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```
1. 安装 .net core
    ```sh
    sudo apt-get install dotnet-sdk-2.1.4
    ```

### 安装Nginx

```sh
sudo apt-get install nginx
```

## 使用Nignx反向代理Asp.Net Core应用程序

### 修改Asp.Net Core程序以设置正确的HttpHeaders

1. 安装`Microsoft.AspNetCore.HttpOverrides`程序包
1. 在Asp.Net Core代码`Startup.Configure`中设置ForwardedHeaders
    ```csharp
    app.UseForwardedHeaders(new ForwardedHeadersOptions
    {
        ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
    });
    app.UseAuthentication();
    ```

### 发布Asp.Net Core应用程序

1. 发布程序
    ```sh
    cd ~/dev/netcoreapp
    dotnet publish -c Release # app folder ~/dev/netcoreapp/bin/Release/netcoreapp2.0/publish
    cd bin/Release/netcoreapp2.0/publish
    cp -rf . ~/netcoreapp
    ```
1. 修改所有者为nginx用户www-data
    ```sh
    cd ~/netcoreapp
    sudo chown -R www-data:www-data .
    ```

### 设置Nginx反向代理

#### 配置反向代理

若Linux服务器仅一个网站，可以直接修改/etc/nginx/sites-available/default配置，修改如下(其中`localhost:5000`为Asp.Net Core应用程序的本地地址)：

```conf
server {
    listen 80;
    server_name www.test.zjf;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### 使用系统服务自动运行

1. 创建一个service文件(~/kestrel-hellomvc.service)，内容如下：
    ```ini
    [Unit]
    Description=Example .NET Web API App running on Ubuntu

    [Service]
    WorkingDirectory=/home/jf/netcoreapp # Asp.Net应用程序所在目录
    ExecStart=/usr/bin/dotnet /home/jf/netcoreapp/hellomvc.dll
    Restart=always
    RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
    SyslogIdentifier=dotnet-example
    User=www-data
    Environment=ASPNETCORE_ENVIRONMENT=Production
    Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

    [Install]
    WantedBy=multi-user.target
    ```
1. 启用系统服务
    ```sh
    sudo mv ~/kestrel-hellomvc.service /etc/systemd/system/kestrel-hellomvc.service
    sudo systemctl enable kestrel-hellomvc.service
    ```
1. 启动系统服务并查看状态
    ```sh
    systemctl start kestrel-hellomvc.service
    systemctl status kestrel-hellomvc.service
    ```
1. 至此，服务器配置完成，运行如下命令启动nginx之后，可以访问 http://localhost 尝试
    ```sh
    sudo service nginx start
    ```

## 启用安全网站访问

### 设置防火墙

Ubuntu有`ufw`可以很简单的设置入站规则，安装和设置如下：

```sh
sudo apt-get install ufw
sudo ufw enable
sudo ufw allow 80/tcp # http
sudo ufw allow 443/tcp # https
```

### 启用https

需要启用nginx的SSL，若软件源中安装的nginx中没有启用SSL，需要[从源码安装Nginx](https://www.nginx.com/resources/admin-guide/installing-nginx-open-source/)，注意：源码安装前确保Linux服务器已安装openssl。

```sh
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download Nginx 1.12.2 or latest
wget http://www.nginx.org/download/nginx-1.12.2.tar.gz
tar zxf nginx-1.12.2.tar.gz
cd nginx-1.12.2
./configure --with-http_ssl_module --with-stream --with-mail=dynamic
make
sudo make install
```

修改nginx配置已使得Asp.Net Core App支持https：

1. 使用openssl生成证书
    ```sh
    openssl req -x509 -subj /CN=www.test.zjf -days 365 -set_serial 2 -newkey rsa:2048 -keyout /etc/nginx/cert.key -nodes -out /etc/nginx/cert.pem
    ```
1. 修改如上的nginx网站配置
    ```conf
    server {
        listen 80;
        listen 443; # https端口
        server_name www.test.zjf;

        ssl_certificate /etc/nginx/cert.pem; # SSL证书
        ssl_certificate_key /etc/nginx/cert.key; # SSL Key

        location / {
            proxy_pass http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection keep-alive;
            proxy_set_header Host $http_host;
            proxy_cache_bypass $http_upgrade;
        }
    }
    ```
1. 运行如下命令重启nginx，就可以访问https://www.test.zjf
    ```sh
    sudo service nginx restart
    ```

## 参考资料

1. [Host ASP.NET Core on Linux with Nginx](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?tabs=aspnetcore2x)
1. [Tutorial: Proxying .NET Core and Kestrel with NGINX and NGINX Plus](https://www.nginx.com/blog/tutorial-proxy-net-core-kestrel-nginx-plus/)
1. [INSTALLING NGINX OPEN SOURCE](https://www.nginx.com/resources/admin-guide/installing-nginx-open-source/)