# 2020_jitsi_meet
How To Jitsi Meet Instalation (Surabaya, 2020-05-05) <br>
<br>
## Case 2 - Install Jitsi Meet on Host O/S Centos 7 (via docker images) 
**2.01. Prepare Host O/S Centos 7 (Do step 1.01 - 1.04) below ...** <br>
**2.02. Use Docker Images from https://hub.docker.com/_/ubuntu?tab=tags** <br>
docker pull ubuntu:20.04 <br>
<br>
docker run --name=ub20vpsmeet -d -it -p 2222:22/tcp -p 80:80/tcp -p 443:443/tcp -p 10000:10000/udp --privileged -v /run/dbus/system_bus_socket:/run/dbus/system_bus_socket ubuntu:20.04 /bin/bash
<br>
docker ps -a <br>
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                                                                                      NAMES
f00ed2c7ab64        ubuntu:20.04        "/bin/bash"         8 seconds ago       Up 7 seconds        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:10000->10000/udp, 0.0.0.0:2222->22/tcp   ub20vpsmeet
```
sudo netstat -plntu <br>
```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp6       0      0 :::443                  :::*                    LISTEN      59924/docker-proxy  
tcp6       0      0 :::2222                 :::*                    LISTEN      59946/docker-proxy  
tcp6       0      0 :::80                   :::*                    LISTEN      59935/docker-proxy  
udp6       0      0 :::10000                :::*                                59913/docker-proxy  
```
docker exec -it ub20vpsmeet /bin/bash <br>
<br>
apt update <br>
apt list --upgradable <br>
apt full-upgrade <br>
apt clean <br>
apt autoremove <br>
apt autoclean <br>
<br>
apt install tzdata <br>
date <br>
```
Sat May  9 10:21:18 WIB 2020
```
apt install sudo <br>
adduser bayu <br>
usermod -aG sudo bayu <br>
<br>
apt install gnupg apt-transport-https <br>
apt install iputils-ping net-tools wget git unzip nano curl elinks lynx python3 <br>
apt-get install openssh-server openssh-client openssh-sftp-server <br>
<br>
/usr/sbin/sshd <br>
```
Missing privilege separation directory: /run/sshd <br>
```
mkdir /var/run/sshd <br>
/etc/init.d/ssh restart <br>
<br>
netstat -plntu <br>
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      4670/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      4670/sshd        
```
**exit** <br>
**(Exit from Root User Terminal - Docker Container)** <br>
<br>
ssh -p 2222 bayu@localhost <br>
```
bayu@localhost's password: 1234567x
```
(Don't forget change user bayu passwd & add another sudo user) <br>
<br>
sudo nano /etc/hosts <br>
```
127.0.0.1	localhost 
#--- 172.17.0.2     5b502ea004ba  
#--- Change the setting below to suit your condition 
139.99.90.116    vpsmeet.idjvnix.com    vpsmeet 
```
sudo nano /etc/hostname <br>
```
#--- Change the setting below to suit your condition 
vpsmeet.idjvnix.com
```
sudo hostname vpsmeet.idjvnix.com  <br>
<br>
hostname <br>
```
vpsmeet.idjvnix.com 
```
#--- Change the command below to suit your condition <br>
ping vpsmeet <br>
ping vpsmeet.idjvnix.com <br>
<br>

**2.03. Prepare SSL Certificate for Your Domain** <br>
sudo netstat -plntu <br>
```
Active Internet connections (only servers) <br>
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name  
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      204/sshd: /usr/sbin 
tcp6       0      0 :::22                   :::*                    LISTEN      204/sshd: /usr/sbin 
```
pwd <br>
```
/home/bayu
```
ls -lh <br>
```
total 28K <br>
-rw-rw-r-- 1 bayu bayu 1.7K May  8 06:17 ca_bundle.crt 
-rw-rw-r-- 1 bayu bayu 1.9K May  8 06:17 certificate.crt 
-rw-rw-r-- 1 bayu bayu 1.7K May  8 06:17 private.key 
-rw-rw-r-- 1 bayu bayu 5.5K May  8 06:19 sslforfree.zip 
```
sudo cp certificate.crt /etc/ssl/certs/vpsmeet.idjvnix.com.crt <br>
sudo cp private.key /etc/ssl/private/vpsmeet.idjvnix.com.key <br>
<br>

**2.04. Install & Configure Jitsi-Meet** <br>
Before Install Jitsi-Meet Remember Your Own SSL certificate Location, <br>
```
Hostname: vpsmeet.idjvnix.com
Full local server path to the SSL key file: /etc/ssl/private/vpsmeet.idjvnix.com.key 
Full local server path to the SSL certificate file: /etc/ssl/certs/vpsmeet.idjvnix.com.crt 
```
sudo echo 'deb https://download.jitsi.org stable/' | sudo tee /etc/apt/sources.list.d/jitsi-stable.list <br>
sudo wget -qO - https://download.jitsi.org/jitsi-key.gpg.key | sudo apt-key add - <br>
<br>
sudo apt update <br>
sudo apt install jitsi-meet <br>
<br>
```
....... 
debconf: falling back to frontend: Readline 
Configuring jitsi-videobridge2 
------------------------------ 
The jitsi-videobridge package needs the DNS hostname of your instance. 
Hostname: vpsmeet.idjvnix.com 
....... 
debconf: falling back to frontend: Readline 
Configuring jitsi-meet-web-config 
--------------------------------- 
Jitsi Meet is best to be set up with an SSL certificate. 
Having no certificate, a self-signed one will be generated. 
By choosing self-signed you will later have a chance to install Let’s Encrypt certificates. 
Having a certificate signed by a recognised CA, 
it can be uploaded on the server and point its location. 
The default filenames will be /etc/ssl/--domain.name--.key for the key 
and /etc/ssl/--domain.name--.crt for the certificate. 
  1. Generate a new self-signed certificate 
     (You will later get a chance to obtain a Let's encrypt certificate) 
  2. I want to use my own certificate 
SSL certificate for the Jitsi Meet instance 2 
The full path to the SSL key file on the server. 
If it has not been uploaded, now is a good time to do so. 
Full local server path to the SSL key file: /etc/ssl/private/vpsmeet.idjvnix.com.key 
The full path to the SSL certificate file on the server. 
If you haven't uploaded it, now is a good time to upload it in another console. 
Full local server path to the SSL certificate file: /etc/ssl/certs/vpsmeet.idjvnix.com.crt 
.......
debconf: falling back to frontend: Readline 
------------------------------------------------ 
turnserver is listening on tcp 4445 as other nginx sites use port 443 
------------------------------------------------ 
invoke-rc.d: could not determine current runlevel 
invoke-rc.d: policy-rc.d denied execution of restart. 
invoke-rc.d: could not determine current runlevel 
invoke-rc.d: policy-rc.d denied execution of restart. 
Processing triggers for systemd (245.4-4ubuntu3) ... 
Processing triggers for libc-bin (2.31-0ubuntu9) ... 
....... 
```
sudo /etc/init.d/prosody restart <br>
sudo /etc/init.d/jitsi-videobridge2 restart <br>
sudo /etc/init.d/jicofo restart <br>
sudo /etc/init.d/nginx restart <br>
<br>
sudo netstat -plntu <br>
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:5280            0.0.0.0:*               LISTEN      9547/lua5.2         
tcp        0      0 127.0.0.1:5347          0.0.0.0:*               LISTEN      9547/lua5.2         
tcp        0      0 0.0.0.0:5222            0.0.0.0:*               LISTEN      9547/lua5.2         
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      9818/nginx: master  
tcp        0      0 0.0.0.0:5269            0.0.0.0:*               LISTEN      9547/lua5.2         
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      4871/sshd: /usr/sbi 
tcp        0      0 0.0.0.0:8888            0.0.0.0:*               LISTEN      9691/java           
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      9818/nginx: master  
tcp        0      0 0.0.0.0:4444            0.0.0.0:*               LISTEN      9818/nginx: master  
tcp6       0      0 :::5280                 :::*                    LISTEN      9547/lua5.2         
tcp6       0      0 :::5222                 :::*                    LISTEN      9547/lua5.2         
tcp6       0      0 :::80                   :::*                    LISTEN      9818/nginx: master  
tcp6       0      0 :::5269                 :::*                    LISTEN      9547/lua5.2         
tcp6       0      0 :::22                   :::*                    LISTEN      4871/sshd: /usr/sbi 
tcp6       0      0 :::443                  :::*                    LISTEN      9818/nginx: master  
tcp6       0      0 :::4444                 :::*                    LISTEN      9818/nginx: master  
udp        0      0 0.0.0.0:35352           0.0.0.0:*                           9691/java           
udp        0      0 0.0.0.0:5000            0.0.0.0:*                           9614/java           
udp        0      0 172.17.0.2:10000        0.0.0.0:*                           9614/java           
udp6       0      0 :::5000                 :::*                                9614/java           
```
**exit** <br>
**(Exit from Normal User Terminal - Docker Container)** <br>
<br>
sudo netstat -plntu <br>
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp6       0      0 :::443                  :::*                    LISTEN      59924/docker-proxy  
tcp6       0      0 :::2222                 :::*                    LISTEN      59946/docker-proxy  
tcp6       0      0 :::80                   :::*                    LISTEN      59935/docker-proxy  
udp6       0      0 :::10000                :::*                                59913/docker-proxy  
```

**2.05. Backup Images to The Cloud (https://hub.docker.com/)** <br>
docker ps -a <br>
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                                                                                      NAMES
f00ed2c7ab64        ubuntu:20.04        "/bin/bash"         About an hour ago   Up About an hour    0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:10000->10000/udp, 0.0.0.0:2222->22/tcp   ub20vpsmeet
```
docker kill ub20vpsmeet <br>
<br>
docker ps -a <br>
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
f00ed2c7ab64        ubuntu:20.04        "/bin/bash"         About an hour ago   Exited (137) 26 seconds ago                       ub20vpsmeet
```
docker ps -lq <br>
```
f00ed2c7ab64
```
docker login <br>
<br>
docker commit -a bayu_ub20vpsmeet f00ed2c7ab64 bayusetyatmoko/ub20vpsmeet <br>
docker commit -a bayu_ub20vpsmeet f00ed2c7ab64 bayusetyatmoko/ub20vpsmeet:default <br>
<br>
docker images <br>
```
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
bayusetyatmoko/ub20vpsmeet   default             119436365577        45 seconds ago       605MB
bayusetyatmoko/ub20vpsmeet   latest              a13de9e550e9        About a minute ago   605MB
ubuntu                       20.04               1d622ef86b13        2 weeks ago          73.9MB
```
docker push bayusetyatmoko/ub20vpsmeet <br>
docker push bayusetyatmoko/ub20vpsmeet:default <br>
<br>
**[done].** <br>
<br>

## Case 1 - Install Jitsi Meet on Host O/S Centos 7 (via docker compose) 
**1.01. Prepare Centos Repo & Install Tools** <br>
sudo yum check-update <br>
sudo yum install nano python3 curl elinks lynx<br>
sudo yum install git <br>
<br>
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash <br>
export NVM_DIR="$HOME/.nvm" <br>
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" <br>
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" <br>
<br>
command -v nvm <br>
nvm ls-remote <br>
nvm install v14.2.0 <br>
node --version <br>
<br>
nano app.js <br>
```
const http = require('http'); 
const hostname = '0.0.0.0'; 
const port = 3000; 
const server = http.createServer((req, res) => { 
  res.statusCode = 200; 
  res.setHeader('Content-Type', 'text/plain'); 
  res.end('Hello World'); 
}); 
server.listen(port, hostname, () => { 
  console.log(`Server running at http://${hostname}:${port}/`);
}); 
```
node app.js  <br>
```
Server running at http://0.0.0.0:3000/  
(To Stop Service Nodejs, Using "Ctrl + c") 
```

**1.02. Prepare Hosts & Hostname (FQDN)** <br>
sudo nano /etc/hosts <br>
```
127.0.0.1	localhost <br>
#--- Change the setting below to suit your condition 
139.99.90.116 	    vpsmeet.idjvnix.com      vpsmeet 
```
#--- Change the command below to suit your condition <br>
sudo hostnamectl set-hostname vpsmeet.idjvnix.com <br>
<br>
sudo hostnamectl status <br>
<br>
hostname <br>
```
vpsmeet.idjvnix.com 
```
#--- Change the command below to suit your condition <br>
ping vpsmeet <br>
ping vpsmeet.idjvnix.com <br>
<br>

**1.03. Prepare Timezone, Date & NTP Synchronized** <br>
sudo timedatectl list-timezones |grep Asia <br>
sudo timedatectl set-timezone Asia/Jakarta <br>
sudo timedatectl set-ntp on <br>
sudo systemctl restart systemd-timedated.service <br>
<br>
sudo timedatectl status <br>
```
      Local time: Thu 2020-04-30 06:10:00 WIB 
  Universal time: Wed 2020-04-29 23:10:00 UTC 
        RTC time: Wed 2020-04-29 23:10:00 
       Time zone: Asia/Jakarta (WIB, +0700) 
     NTP enabled: yes 
NTP synchronized: yes 
 RTC in local TZ: no 
      DST active: n/a 
```
date <br>
```
Thu Apr 30 06:11:58 WIB 2020 
```

**1.04. Prepare Docker Repo, Install Docker & Install Docker Compose** <br>
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine <br>
<br>
sudo yum check-update <br>
sudo yum install -y yum-utils device-mapper-persistent-data lvm2 <br>
<br>
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo <br>
<br>
sudo yum check-update <br>
sudo yum install docker-ce docker-ce-cli containerd.io <br>
sudo systemctl start docker <br>
sudo systemctl status docker <br>
<br>
sudo usermod -aG docker bayu <br>
newgrp docker <br>
docker version <br>
<br>
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose <br>
<br>
sudo chmod +x /usr/local/bin/docker-compose <br>
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose <br>
docker-compose version <br>
<br>

**1.05. Install Jitsi Meet (via Docker Compose)** <br>
git clone https://github.com/jitsi/docker-jitsi-meet && cd docker-jitsi-meet <br>
<br>
pwd <br>
```
/home/bayu/docker-jitsi-meet 
```
cp env.example .env <br>
./gen-passwords.sh <br>
<br>
mkdir -p ~/.jitsi-meet-cfg/{web/letsencrypt,transcripts,prosody,jicofo,jvb,jigasi,jibri} <br>
<br>
ls -lah /home/bayu/ |grep jitsi <br>
```
drwxrwxr-x  14 bayu bayu 4,0K Apr 30 07:18 docker-jitsi-meet <br>
drwxrwxr-x   9 bayu bayu 4,0K Apr 30 07:19 .jitsi-meet-cfg <br>
```
nano .env <br>
```
HTTP_PORT=80 <br>
HTTPS_PORT=443 <br>
TZ=Asia/Jakarta <br>
#--- Change the setting below to suit your condition <br>
PUBLIC_URL=https://vpsmeet.idjvnix.com <br>
ENABLE_LETSENCRYPT=1 <br>
#--- Change the setting below to suit your condition <br>
LETSENCRYPT_DOMAIN=vpsmeet.idjvnix.com <br>
#--- Change the setting below to suit your condition <br>
LETSENCRYPT_EMAIL=bayuzzz@gmail.com <br>
DISABLE_HTTPS=0 <br>
ENABLE_HTTP_REDIRECT=1 <br>
```
#--- To stop command below, using "ctrl+c" <br>
docker-compose up <br>
<br>
#--- To stop command below (as daemon), using "docker-compose down" <br>
docker-compose up -d <br>
<br>
docker-compose ps -a <br>
```
           Name               Command   State                        Ports                        <br>
------------------------------------------------------------------------------------------------  <br>
docker-jitsi-meet_jicofo_1    /init     Up                                                        <br>
docker-jitsi-meet_jvb_1       /init     Up      0.0.0.0:10000->10000/udp, 0.0.0.0:4443->4443/tcp  <br>
docker-jitsi-meet_prosody_1   /init     Up      5222/tcp, 5269/tcp, 5280/tcp, 5347/tcp            <br>
docker-jitsi-meet_web_1       /init     Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp          <br>
```

**Notes :** <br>
a. If you edit file ".env" then you must remove ~/.jitsi-meet-cfg then recreate ~/.jitsi-meet-cfg <br> 
<br>
b. Backup Source Code & Config Jitsi-Meet Application using this command <br> 
<br>
sudo tar -czvf backup_20200508_docker-jitsi-meet.tar.gz docker-jitsi-meet/ <br>
```
docker-jitsi-meet/ 
docker-jitsi-meet/web/ 
docker-jitsi-meet/web/Makefile 
docker-jitsi-meet/web/rootfs/ 
docker-jitsi-meet/web/rootfs/etc/ 
docker-jitsi-meet/web/rootfs/etc/services.d/ 
docker-jitsi-meet/web/rootfs/etc/services.d/cron/ 
docker-jitsi-meet/web/rootfs/etc/services.d/cron/run 
docker-jitsi-meet/web/rootfs/etc/services.d/nginx/ 
docker-jitsi-meet/web/rootfs/etc/services.d/nginx/run 
docker-jitsi-meet/web/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/web/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/web/rootfs/defaults/ 
docker-jitsi-meet/web/rootfs/defaults/ssl.conf 
docker-jitsi-meet/web/rootfs/defaults/meet.conf 
docker-jitsi-meet/web/rootfs/defaults/letsencrypt-renew 
docker-jitsi-meet/web/rootfs/defaults/nginx.conf 
docker-jitsi-meet/web/rootfs/defaults/default 
docker-jitsi-meet/web/Dockerfile 
docker-jitsi-meet/jigasi/ 
docker-jitsi-meet/jigasi/Makefile 
docker-jitsi-meet/jigasi/rootfs/ 
docker-jitsi-meet/jigasi/rootfs/etc/ 
docker-jitsi-meet/jigasi/rootfs/etc/services.d/ 
docker-jitsi-meet/jigasi/rootfs/etc/services.d/jigasi/ 
docker-jitsi-meet/jigasi/rootfs/etc/services.d/jigasi/run 
docker-jitsi-meet/jigasi/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/jigasi/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/jigasi/rootfs/defaults/ 
docker-jitsi-meet/jigasi/rootfs/defaults/sip-communicator.properties 
docker-jitsi-meet/jigasi/rootfs/defaults/logging.properties 
docker-jitsi-meet/jigasi/Dockerfile 
docker-jitsi-meet/.env.bak 
docker-jitsi-meet/README.md 
docker-jitsi-meet/base-java/ 
docker-jitsi-meet/base-java/Makefile 
docker-jitsi-meet/base-java/Dockerfile 
docker-jitsi-meet/etherpad/ 
docker-jitsi-meet/etherpad/Makefile 
docker-jitsi-meet/etherpad/rootfs/ 
docker-jitsi-meet/etherpad/rootfs/defaults/ 
docker-jitsi-meet/etherpad/rootfs/defaults/settings.json 
docker-jitsi-meet/etherpad/Dockerfile 
docker-jitsi-meet/jicofo/ 
docker-jitsi-meet/jicofo/Makefile 
docker-jitsi-meet/jicofo/rootfs/ 
docker-jitsi-meet/jicofo/rootfs/etc/ 
docker-jitsi-meet/jicofo/rootfs/etc/services.d/ 
docker-jitsi-meet/jicofo/rootfs/etc/services.d/jicofo/ 
docker-jitsi-meet/jicofo/rootfs/etc/services.d/jicofo/run 
docker-jitsi-meet/jicofo/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/jicofo/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/jicofo/rootfs/defaults/ 
docker-jitsi-meet/jicofo/rootfs/defaults/sip-communicator.properties 
docker-jitsi-meet/jicofo/rootfs/defaults/logging.properties 
docker-jitsi-meet/jicofo/Dockerfile 
docker-jitsi-meet/CHANGELOG.md 
docker-jitsi-meet/gen-passwords.sh 
docker-jitsi-meet/.gitignore 
docker-jitsi-meet/jigasi.yml 
docker-jitsi-meet/base/ 
docker-jitsi-meet/base/Makefile 
docker-jitsi-meet/base/rootfs/ 
docker-jitsi-meet/base/rootfs/etc/ 
docker-jitsi-meet/base/rootfs/etc/services.d/ 
docker-jitsi-meet/base/rootfs/etc/services.d/.gitkeep 
docker-jitsi-meet/base/rootfs/etc/apt/ 
docker-jitsi-meet/base/rootfs/etc/apt/apt.conf.d/ 
docker-jitsi-meet/base/rootfs/etc/apt/apt.conf.d/99local 
docker-jitsi-meet/base/rootfs/etc/fix-attrs.d/ 
docker-jitsi-meet/base/rootfs/etc/fix-attrs.d/.gitkeep 
docker-jitsi-meet/base/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/base/rootfs/etc/cont-init.d/.gitkeep 
docker-jitsi-meet/base/rootfs/etc/cont-init.d/01-set-timezone 
docker-jitsi-meet/base/rootfs/etc/cont-finish.d/ 
docker-jitsi-meet/base/rootfs/etc/cont-finish.d/.gitkeep 
docker-jitsi-meet/base/rootfs/usr/ 
docker-jitsi-meet/base/rootfs/usr/bin/ 
docker-jitsi-meet/base/rootfs/usr/bin/apt-cleanup 
docker-jitsi-meet/base/rootfs/usr/bin/tpl 
docker-jitsi-meet/base/rootfs/usr/bin/apt-dpkg-wrap 
docker-jitsi-meet/base/Dockerfile 
docker-jitsi-meet/docker-compose.yml 
docker-jitsi-meet/Makefile 
docker-jitsi-meet/resources/ 
docker-jitsi-meet/resources/docker-jitsi-meet.png 
docker-jitsi-meet/resources/jitsi-docker.png 
docker-jitsi-meet/resources/docker-jitsi-meet.xml 
docker-jitsi-meet/jvb/ 
docker-jitsi-meet/jvb/Makefile 
docker-jitsi-meet/jvb/rootfs/ 
docker-jitsi-meet/jvb/rootfs/etc/ 
docker-jitsi-meet/jvb/rootfs/etc/services.d/ 
docker-jitsi-meet/jvb/rootfs/etc/services.d/jvb/ 
docker-jitsi-meet/jvb/rootfs/etc/services.d/jvb/run 
docker-jitsi-meet/jvb/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/jvb/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/jvb/rootfs/defaults/ 
docker-jitsi-meet/jvb/rootfs/defaults/sip-communicator.properties 
docker-jitsi-meet/jvb/rootfs/defaults/logging.properties 
docker-jitsi-meet/jvb/Dockerfile 
docker-jitsi-meet/.git/ 
docker-jitsi-meet/.git/index 
docker-jitsi-meet/.git/config 
docker-jitsi-meet/.git/logs/ 
docker-jitsi-meet/.git/logs/HEAD 
docker-jitsi-meet/.git/logs/refs/ 
docker-jitsi-meet/.git/logs/refs/remotes/ 
docker-jitsi-meet/.git/logs/refs/remotes/origin/ 
docker-jitsi-meet/.git/logs/refs/remotes/origin/HEAD 
docker-jitsi-meet/.git/logs/refs/heads/ 
docker-jitsi-meet/.git/logs/refs/heads/master
docker-jitsi-meet/.git/info/ 
docker-jitsi-meet/.git/info/exclude 
docker-jitsi-meet/.git/hooks/ 
docker-jitsi-meet/.git/hooks/commit-msg.sample 
docker-jitsi-meet/.git/hooks/pre-rebase.sample 
docker-jitsi-meet/.git/hooks/post-update.sample 
docker-jitsi-meet/.git/hooks/prepare-commit-msg.sample 
docker-jitsi-meet/.git/hooks/pre-applypatch.sample 
docker-jitsi-meet/.git/hooks/update.sample 
docker-jitsi-meet/.git/hooks/pre-commit.sample 
docker-jitsi-meet/.git/hooks/pre-push.sample 
docker-jitsi-meet/.git/hooks/pre-push.sample 
docker-jitsi-meet/.git/hooks/applypatch-msg.sample 
docker-jitsi-meet/.git/branches/ 
docker-jitsi-meet/.git/objects/ 
docker-jitsi-meet/.git/objects/info/ 
docker-jitsi-meet/.git/objects/pack/ 
docker-jitsi-meet/.git/objects/pack/pack-9bcad6a95d237ba1f9246ce08f6acb00a987280f.idx 
docker-jitsi-meet/.git/objects/pack/pack-9bcad6a95d237ba1f9246ce08f6acb00a987280f.pack 
docker-jitsi-meet/.git/HEAD 
docker-jitsi-meet/.git/refs/ 
docker-jitsi-meet/.git/refs/tags/ 
docker-jitsi-meet/.git/refs/remotes/ 
docker-jitsi-meet/.git/refs/remotes/origin/ 
docker-jitsi-meet/.git/refs/remotes/origin/HEAD 
docker-jitsi-meet/.git/refs/heads/ 
docker-jitsi-meet/.git/refs/heads/master 
docker-jitsi-meet/.git/description 
docker-jitsi-meet/.git/packed-refs 
docker-jitsi-meet/jibri/ 
docker-jitsi-meet/jibri/Makefile 
docker-jitsi-meet/jibri/rootfs/ 
docker-jitsi-meet/jibri/rootfs/etc/ 
docker-jitsi-meet/jibri/rootfs/etc/opt/ 
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/ 
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/ 
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/managed/ 
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/managed/managed_policies.json 
docker-jitsi-meet/jibri/rootfs/etc/services.d/ 
docker-jitsi-meet/jibri/rootfs/etc/services.d/20-icewm/ 
docker-jitsi-meet/jibri/rootfs/etc/services.d/20-icewm/run 
docker-jitsi-meet/jibri/rootfs/etc/services.d/10-xorg/ 
docker-jitsi-meet/jibri/rootfs/etc/services.d/10-xorg/run 
docker-jitsi-meet/jibri/rootfs/etc/services.d/30-jibri/ 
docker-jitsi-meet/jibri/rootfs/etc/services.d/30-jibri/run 
docker-jitsi-meet/jibri/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/jibri/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/jibri/rootfs/defaults/ 
docker-jitsi-meet/jibri/rootfs/defaults/config.json 
docker-jitsi-meet/jibri/rootfs/defaults/logging.properties 
docker-jitsi-meet/jibri/Dockerfile 
docker-jitsi-meet/.env 
docker-jitsi-meet/LICENSE 
docker-jitsi-meet/jibri.yml 
docker-jitsi-meet/prosody/ 
docker-jitsi-meet/prosody/Makefile 
docker-jitsi-meet/prosody/rootfs/ 
docker-jitsi-meet/prosody/rootfs/etc/ 
docker-jitsi-meet/prosody/rootfs/etc/services.d/ 
docker-jitsi-meet/prosody/rootfs/etc/services.d/10-saslauthd/ 
docker-jitsi-meet/prosody/rootfs/etc/services.d/10-saslauthd/run 
docker-jitsi-meet/prosody/rootfs/etc/services.d/prosody/ 
docker-jitsi-meet/prosody/rootfs/etc/services.d/prosody/run 
docker-jitsi-meet/prosody/rootfs/etc/sasl/ 
docker-jitsi-meet/prosody/rootfs/etc/sasl/xmpp.conf 
docker-jitsi-meet/prosody/rootfs/etc/cont-init.d/ 
docker-jitsi-meet/prosody/rootfs/etc/cont-init.d/10-config 
docker-jitsi-meet/prosody/rootfs/defaults/ 
docker-jitsi-meet/prosody/rootfs/defaults/conf.d/ 
docker-jitsi-meet/prosody/rootfs/defaults/conf.d/jitsi-meet.cfg.lua 
docker-jitsi-meet/prosody/rootfs/defaults/saslauthd.conf 
docker-jitsi-meet/prosody/rootfs/defaults/prosody.cfg.lua 
docker-jitsi-meet/prosody/Dockerfile 
docker-jitsi-meet/examples/ 
docker-jitsi-meet/examples/README.md 
docker-jitsi-meet/examples/traefik/ 
docker-jitsi-meet/examples/traefik/README.md 
docker-jitsi-meet/examples/traefik/docker-compose.yml 
docker-jitsi-meet/examples/kubernetes/ 
docker-jitsi-meet/examples/kubernetes/README.md 
docker-jitsi-meet/examples/kubernetes/deployment.yaml 
docker-jitsi-meet/examples/kubernetes/web-service.yaml 
docker-jitsi-meet/examples/kubernetes/jvb-service.yaml 
docker-jitsi-meet/examples/traefik-v2/ 
docker-jitsi-meet/examples/traefik-v2/README.md 
docker-jitsi-meet/examples/traefik-v2/docker-compose.yml 
docker-jitsi-meet/env.example 
docker-jitsi-meet/etherpad.yml 
```
sudo tar -czvf backup_20200508_jitsi-meet-cfg.tar.gz .jitsi-meet-cfg/ <br>
```
.jitsi-meet-cfg/ 
.jitsi-meet-cfg/web/ 
.jitsi-meet-cfg/web/le-renew.log 
.jitsi-meet-cfg/web/keys/ 
.jitsi-meet-cfg/web/interface_config.js 
.jitsi-meet-cfg/web/letsencrypt/ 
.jitsi-meet-cfg/web/letsencrypt/csr/ 
.jitsi-meet-cfg/web/letsencrypt/csr/0000_csr-certbot.pem 
.jitsi-meet-cfg/web/letsencrypt/accounts/ 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/ 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/ 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/ 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/private_key.json 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/meta.json 
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/regr.json 
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/ 
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/deploy/ 
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/post/ 
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/pre/ 
.jitsi-meet-cfg/web/letsencrypt/keys/ 
.jitsi-meet-cfg/web/letsencrypt/keys/0000_key-certbot.pem 
.jitsi-meet-cfg/web/letsencrypt/archive/ 
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/ 
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/fullchain1.pem 
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/chain1.pem 
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/cert1.pem 
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/privkey1.pem 
.jitsi-meet-cfg/web/letsencrypt/renewal/ 
.jitsi-meet-cfg/web/letsencrypt/renewal/vpsmeet.idjvnix.com.conf 
.jitsi-meet-cfg/web/letsencrypt/live/ 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/ 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/fullchain.pem 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/cert.pem 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/chain.pem 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/README 
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/privkey.pem 
.jitsi-meet-cfg/web/letsencrypt/live/README 
.jitsi-meet-cfg/web/config.js 
.jitsi-meet-cfg/web/nginx/ 
.jitsi-meet-cfg/web/nginx/ssl.conf 
.jitsi-meet-cfg/web/nginx/meet.conf 
.jitsi-meet-cfg/web/nginx/nginx.conf 
.jitsi-meet-cfg/web/nginx/site-confs/ 
.jitsi-meet-cfg/web/nginx/site-confs/default 
.jitsi-meet-cfg/jigasi/ 
.jitsi-meet-cfg/transcripts/ 
.jitsi-meet-cfg/jicofo/ 
.jitsi-meet-cfg/jicofo/sip-communicator.properties 
.jitsi-meet-cfg/jicofo/logging.properties 
.jitsi-meet-cfg/jvb/ 
.jitsi-meet-cfg/jvb/sip-communicator.properties 
.jitsi-meet-cfg/jvb/logging.properties 
.jitsi-meet-cfg/jibri/ 
.jitsi-meet-cfg/prosody/ 
.jitsi-meet-cfg/prosody/config/ 
.jitsi-meet-cfg/prosody/config/data/ 
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/ 
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/accounts/ 
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/accounts/recorder.dat 
.jitsi-meet-cfg/prosody/config/data/.rnd 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/ 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/ 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jibri.dat 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jvb.dat 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jigasi.dat 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/focus.dat 
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/offline/ 
.jitsi-meet-cfg/prosody/config/conf.d/ 
.jitsi-meet-cfg/prosody/config/conf.d/jitsi-meet.cfg.lua 
.jitsi-meet-cfg/prosody/config/saslauthd.conf 
.jitsi-meet-cfg/prosody/config/certs/ 
.jitsi-meet-cfg/prosody/config/certs/auth.meet.jitsi.key 
.jitsi-meet-cfg/prosody/config/certs/meet.jitsi.crt 
.jitsi-meet-cfg/prosody/config/certs/meet.jitsi.key 
.jitsi-meet-cfg/prosody/config/certs/auth.meet.jitsi.crt 
.jitsi-meet-cfg/prosody/config/prosody.cfg.lua 
.jitsi-meet-cfg/prosody/prosody-plugins-custom/ 
```

**Refference :** <br>
https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md <br>
https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md <br>
https://github.com/jitsi/docker-jitsi-meet/blob/master/README.md <br>
https://github.com/jitsi/jicofo#secure-domain <br>
<br>
