## 代理服务器  
ssh root@120.48.158.208  

查看端口占用：netstat -tuln  
实时监听端口：tcpdump -i any port 端口号  
查看进程信息：lsof -i  
查看套接字统计：ss -tuln  

## 在服务器上安装 shadowsocks 客户端
- 用于 v2ray 安装加速

### 安装教程：[Linux 使用 Shadowsocks 设置教程 #命令行客户端](https://shadowsockshelp.github.io/Shadowsocks/linux.html#%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF)   

### 配置文件
```
{
    "server":"pvcc01.ptrecord.com",
    "server_port":63125,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"dCOTCu",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

### 报错修复方法
- 查看：[ubuntu 22.04 如何安装shadowsocks服务端](http://nfvschool.cn/archives/ubuntu-22.04-ru-he-an-zhuang-shadowsocksfu-wu-duan)   

## 安装 gsettings  
sudo apt-get install gsettings-desktop-schemas  

### 使用 gsettings 设置系统代理  
gsettings set org.gnome.system.proxy mode 'manual'  
gsettings set org.gnome.system.proxy.socks host '127.0.0.1'  
gsettings set org.gnome.system.proxy.socks port 1080  

### 将系统代理设置还原为正常状态  
gsettings set org.gnome.system.proxy mode 'none'  

## 安装 v2ray

安装教程：https://guide.v2fly.org/prep/start.html  
程序地址：/usr/local/bin/v2ray  
配置文件：/usr/local/etc/v2ray/config.json  
系统服务文件：/etc/systemd/system/v2ray.service  
测试 v2ray 配置文件：v2ray test -config /usr/local/etc/v2ray/config.json  

## 安装 v2rayA
安装教程：https://v2raya.org/docs/prologue/introduction/  
系统服务文件：/lib/systemd/system/v2raya.service

## 安装 nginx  

### 配置 nginx 反向代理
```
stream {
    log_format stream_log '$remote_addr [$time_local] protocol: $protocol, '
                          'connection status: $status, bytes sent: $bytes_sent, '
                          'bytes received: $bytes_received, server: $server_addr, '
                          'client: $remote_addr';

    access_log /var/log/nginx/stream_access.log stream_log;

    server {
        listen 127.0.0.1:6666 udp;
        proxy_connect_timeout 6s;       # 与被代理服务器建立连接的超时时间
        proxy_timeout 12s;              # 获取被代理服务器响应超时时间
        proxy_socket_keepalive on;      # 开启心跳检测

        proxy_pass 180.76.243.7:7777;
    }

    server {
        listen 127.0.0.1:6667 udp;
        proxy_connect_timeout 6s;       # 与被代理服务器建立连接的超时时间
        proxy_timeout 12s;              # 获取被代理服务器响应超时时间
        proxy_socket_keepalive on;      # 开启心跳检测

        proxy_pass 180.76.243.7:7778;
    }

    server {
        listen 127.0.0.1:8888;
        proxy_connect_timeout 6s;       # 与被代理服务器建立连接的超时时间
        proxy_timeout 12s;              # 获取被代理服务器响应超时时间
        proxy_socket_keepalive on;      # 开启心跳检测

        proxy_pass 180.76.243.7:9999;
    }

    server {
        listen 127.0.0.1:24872;
        proxy_connect_timeout 6s;       # 与被代理服务器建立连接的超时时间
        proxy_timeout 12s;              # 获取被代理服务器响应超时时间
        proxy_socket_keepalive on;      # 开启心跳检测

        proxy_pass 180.76.243.7:35983;
    }
}
```
