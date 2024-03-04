# theisle-evrima-deploy

## 服务器配置
操作系统 ubuntu 22.04  
运行内存 16G  
网络带宽 50M  
处理器   intel 超频  

## 服务器安全规则
### 1、开启 ICMP
### 2、开启 TCP & UDP 端口
#### 必须开启
Theisle Server：7777-7778  
Steam Server：27015-27016  

#### 可以不开启
SteamCMD RCON：8888  
- evrima_kook_bot 和游戏部署在同一台服务器上，如果没有外部调用 rcon 的需要，就不用开启  

#### 不确定是否开启
10000  

## 创建用户

### 1、创建新用户并添加到 "sudoers" 组
sudo add user USER_NAME  
sudo usermod -aG sudo USER_NAME  

### 2、切换到新用户
su - USER_NAME  

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
sudo apt install lib32gcc1  
sudo apt install lib32stdc++6 libc6-i386 libcurl4-gnutls-dev:i386 libsdl2-2.0-0:i386  

### 5、创建目录，下载 SteamCMD 解压至该目录
mkdir theisle_evrima  # 提前创建好安装文件夹  
mkdir steamcmd & cd steamcmd  
curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -  

### 6、运行 ​​SteamCMD
sudo chmod +x steamcmd.sh  
./steamcmd.sh  

## 安装 theisle 测试版 - Evrima

### 1、将 theisle_evrima 文件夹设置为安装目录
Steam> force_install_dir /home/USER_NAME/theisle_evrima  

### 2、使用匿名登录
Steam> login anonymous  

### 3、下载并安装游戏
Steam> app_update 412680 -beta evrima +quit  
Steam> exit  # 安装完成后退出  

### 4、现在服务器已安装，但还需要安装 64 位的 Steam 库
- 服务器希望 64 位版本的 "steamclient.so" 位于 "/home/USER_NAME/.steam/sdk64/steamclient.so"  
- 但它目前位于 "/home/USER_NAME/steamcmd/linux64/steamclient.so" 中  
- 我们只需创建该文件夹并将其移动到那里  
mkdir -p /home/USER_NAME/.steam/sdk64
mv /home/USER_NAME/steamcmd/linux64/steamclient.so /home/USER_NAME/.steam/sdk64/steamclient.so

## 游戏配置及运行

### 1、将创建配置文件所在的文件夹
mkdir /home/USER_NAME/theisle_evrima/TheIsle/Saved  
mkdir /home/USER_NAME/theisle_evrima/TheIsle/Saved/Config  
mkdir /home/USER_NAME/theisle_evrima/TheIsle/Saved/Config/LinuxServer  

### 2、将配置文件放到 LinuxServer 文件夹下
cd /home/USER_NAME/theisle_evrima/TheIsle/Saved/Config/LinuxServer  
wget https://raw.githubusercontent.com/zhulei1991/theisle-evrima-deploy/main/Engine.ini  
wget https://raw.githubusercontent.com/zhulei1991/theisle-evrima-deploy/main/Game.ini  

### 3、启动游戏服务
cd /home/USER_NAME/theisle_evrima  
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
User=USER_NAME
ExecStart=/usr/bin/env /home/USER_NAME/theisle_evrima/./TheIsleServer.sh MultiHome=192.168.1.100?Port=7777?QueryPort=7778 -log
  
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





