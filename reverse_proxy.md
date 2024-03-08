代理服务器  
ssh root@120.48.158.208  

安装 v2ray  
安装 v2rayA  

配置 v2ray 入站规则  
安装 nginx  
配置 nginx 反向代理  

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
