# theisle-evrima-deploy

## 服务器配置
操作系统 ubuntu 22.04  
运行内存 16G  
网络带宽 50M  
处理器   intel 超频  

## 服务器安全规则
### 1、开启 ICMP
### 2、开启 TCP & UDP 端口
Theisle Server：7777-7778  
Steam Server：27015-27016  
SteamCMD RCON：8888  
- evrima_kook_bot 和游戏部署在同一台服务器上，如果没有外部调用 rcon 的需要，就不用开启  

#### 不确定是否开启
10000  

## 创建用户

### 1、创建新用户并添加到 "sudoers" 组
sudo adduser ziyu0209  
sudo usermod -aG sudo ziyu0209  

### 2、切换到新用户
su - ziyu0209  

## 安装 SteamCMD
### 1、更新本地软件包列表
sudo apt-get update  

### 2、升级系统中已安装的软件包到它们的最新版本
sudo apt-get upgrade  

### 3、先启用 multiverse 存储库和 x86 软件包
sudo add-apt-repository multiverse  
sudo dpkg --add-architecture i386  
sudo apt update  

### 4、安装 SteamCMD 的依赖包
sudo apt install lib32gcc1  或者  sudo apt install lib32gcc-s1  或者  sudo apt-get install libgcc1  
sudo apt install lib32stdc++6  
sudo apt install libc6-i386  
sudo apt install libcurl4-gnutls-dev:i386  
sudo apt install libsdl2-2.0-0:i386  

### 5、创建目录，下载 SteamCMD 解压至该目录
mkdir theisle_evrima  # 提前创建好安装文件夹  
mkdir steamcmd  
cd steamcmd  
curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -  

### 6、运行 ​​SteamCMD
sudo chmod +x steamcmd.sh  
./steamcmd.sh  

## 安装 theisle 测试版 - Evrima

### 1、将 theisle_evrima 文件夹设置为安装目录
Steam> force_install_dir /home/ziyu0209/theisle_evrima  

### 2、使用匿名登录
Steam> login anonymous  

### 3、下载并安装游戏
Steam> app_update 412680 -beta evrima +quit  
Steam> exit  # 安装完成后退出  

### 4、现在服务器已安装，但还需要安装 64 位的 Steam 库
- 服务器希望 64 位版本的 "steamclient.so" 位于 "/home/ziyu0209/.steam/sdk64/steamclient.so"  
- 但它目前位于 "/home/ziyu0209/steamcmd/linux64/steamclient.so" 中  
- 我们只需创建该文件夹并将其移动到那里  
mkdir -p /home/ziyu0209/.steam/sdk64  
mv /home/ziyu0209/steamcmd/linux64/steamclient.so /home/ziyu0209/.steam/sdk64/steamclient.so

## 游戏配置及运行

### 1、将创建配置文件所在的文件夹
mkdir /home/ziyu0209/theisle_evrima/TheIsle/Saved  
mkdir /home/ziyu0209/theisle_evrima/TheIsle/Saved/Config  
mkdir /home/ziyu0209/theisle_evrima/TheIsle/Saved/Config/LinuxServer  

### 2、将配置文件放到 LinuxServer 文件夹下
cd /home/ziyu0209/theisle_evrima/TheIsle/Saved/Config/LinuxServer  
wget https://raw.githubusercontent.com/zhulei1991/theisle-evrima-deploy/main/Engine.ini  
wget https://raw.githubusercontent.com/zhulei1991/theisle-evrima-deploy/main/Game.ini  

### 3、启动游戏服务
cd /home/ziyu0209/theisle_evrima  
./TheIsleServer.sh MultiHome=192.168.1.100?Port=7777?QueryPort=7778 -log  

## 创建系统服务

### 1、创建 theisle_evrima.service 文件
sudo vi /etc/systemd/system/theisle_evrima.service

#### 文件内容如下：
  
[Unit]  
Description=TheIsle Evrima Server  
After=network.target  
StartLimitIntervalSec=0  
  
[Service]  
Type=simple  
Restart=always  
RestartSec=1  
User=ziyu0209  
ExecStart=/usr/bin/env /home/ziyu0209/theisle_evrima/./TheIsleServer.sh MultiHome=192.168.1.100?Port=7777?QueryPort=7778 -log
  
[Install]  
WantedBy=multi-user.target  

#### 保存并退出

### 2、重新加载 systemd 守护进程
sudo systemctl daemon-reload  

### 3、设置开机自动启动
systemctl enable theisle_evrima.service  

### 4、启动服务，查看状态
systemctl start theisle_evrima.service  
systemctl status theisle_evrima.service  

### 5、查看日志  
journalctl -u theisle_evrima.service  



## 反向代理服务

游戏服务器：ssh root@180.76.243.7
代理服务器：ssh root@120.48.158.208

### 配置代理服务器

#### 1、进入代理服务器

#### 2、安装 Nginx

安装必备组件:  
sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring  

导入官方 nginx 签名密钥，以便 apt 可以验证包的真实性。获取密钥:  
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg > /dev/null  

要为稳定的 nginx 包设置 apt 存储库，请运行以下命令:  
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" | sudo tee /etc/apt/sources.list.d/nginx.list  

如果您想使用主线 nginx 包，请运行以下命令:  
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" | sudo tee /etc/apt/sources.list.d/nginx.list  

设置存储库固定以更喜欢我们的包而不是系统提供的包:  
echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" | sudo tee /etc/apt/preferences.d/99nginx  

要安装 nginx，请运行以下命令:  
sudo apt update  
sudo apt install nginx  

#### 3、修改 Nginx 配置

#### 4、启动 Nginx

### 配置游戏服务器

游戏服务器正常运行状态下，使用了以下四个端口：
- udp 7777
- udp 7778
- tcp 9999
- tcp 35983

查询 iptables 规则  
sudo iptables -L  
sudo iptables -t nat -L  # 查询nat表中的网络地址转换规则  
sudo iptables -t nat -L --line-numbers  # 同时列出编号  

添加规则  
- 将游戏服务器发出的请求转发到代理服务器上  
sudo iptables -t nat -A PREROUTING -p udp --dport 7777 -j DNAT --to-destination 120.48.158.208:7777  
sudo iptables -t nat -A PREROUTING -p udp --dport 7778 -j DNAT --to-destination 120.48.158.208:7778  
sudo iptables -t nat -A PREROUTING -p tcp --dport 9999 -j DNAT --to-destination 120.48.158.208:9999  
sudo iptables -t nat -A PREROUTING -p tcp --dport 35983 -j DNAT --to-destination 120.48.158.208:35983

安装 iptables-persistent  
- 使用 iptables-persistent 保存端口转发规则  
- 它可以在系统重启后自动恢复 iptables 规则  
sudo apt-get update  
sudo apt-get install iptables-persistent  

修改 iptables 规则后，可以使用以下命令保存  
sudo netfilter-persistent save  

删除规则  
sudo iptables -t nat -D PREROUTING --line-numbers  
例如：sudo iptables -t nat -D PREROUTING 1  






