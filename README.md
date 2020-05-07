# 2020_jitsi_meet
How To Jitsi Meet Instalation (Surabaya, 2020-05-05)

# Case 1 - Install Jitsi Meet on O/S Centos 7 (via docker) 
# 1.01. Prepare Centos Repo & Install Tools
sudo yum check-update <br>
sudo yum install nano python3 curl elinks lynx<br>
sudo yum install git <br>
<br>
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash <br>
export NVM_DIR="$HOME/.nvm" <br>
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" <br>
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" <br>
command -v nvm <br>
nvm ls-remote <br>
nvm install v14.2.0 <br>
node --version <br>
<br>
nano app.js <br>
const http = require('http'); <br>
const hostname = '0.0.0.0'; <br>
const port = 3000; <br>
const server = http.createServer((req, res) => { <br>
  res.statusCode = 200; <br>
  res.setHeader('Content-Type', 'text/plain'); <br>
  res.end('Hello World'); <br>
}); <br>
server.listen(port, hostname, () => { <br>
  console.log(`Server running at http://${hostname}:${port}/`); <br>
}); <br>
<br>
node app.js  <br>
Server running at http://0.0.0.0:3000/  <br>
(To Stop Service Nodejs, Using "Ctrl + c") <br>
<br>

# 1.02. Prepare Hosts & Hostname (FQDN)
sudo nano /etc/hosts <br>
127.0.0.1	localhost <br>
#--- Change the setting below to suit your condition <br>
139.99.90.116 	    vpsmeet.idjvnix.com      vpsmeet <br>
<br>
#--- Change the command below to suit your condition <br>
sudo hostnamectl set-hostname vpsmeet.idjvnix.com <br>
<br>
sudo hostnamectl status <br>
<br>
hostname <br>
vpsmeet.idjvnix.com <br>
<br>
#--- Change the command below to suit your condition <br>
ping vpsmeet <br>
ping vpsmeet.idjvnix.com <br>
<br>

# 1.03. Prepare Timezone, Date & NTP Synchronized
sudo timedatectl list-timezones |grep Asia <br>
sudo timedatectl set-timezone Asia/Jakarta <br>
sudo timedatectl set-ntp on <br>
sudo systemctl restart systemd-timedated.service <br>
<br>
sudo timedatectl status <br>
      Local time: Thu 2020-04-30 06:10:00 WIB <br>
  Universal time: Wed 2020-04-29 23:10:00 UTC <br>
        RTC time: Wed 2020-04-29 23:10:00 <br>
       Time zone: Asia/Jakarta (WIB, +0700) <br>
     NTP enabled: yes <br>
NTP synchronized: yes <br>
 RTC in local TZ: no <br>
      DST active: n/a <br>
<br>
date <br>
Thu Apr 30 06:11:58 WIB 2020 <br>
<br>

# 1.04. Prepare Docker Repo, Install Docker & Install Docker Compose
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
<br>
docker version <br>
<br>
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose <br>
<br>
sudo chmod +x /usr/local/bin/docker-compose <br>
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose <br>
docker-compose version <br>
<br>

# 1.05. Install Jitsi Meet (via Docker)
git clone https://github.com/jitsi/docker-jitsi-meet && cd docker-jitsi-meet <br>
<br>
pwd <br>
/home/bayu/docker-jitsi-meet <br>
<br>
cp env.example .env <br>
./gen-passwords.sh <br>
mkdir -p ~/.jitsi-meet-cfg/{web/letsencrypt,transcripts,prosody,jicofo,jvb,jigasi,jibri} <br>
<br>
ls -lah /home/bayu/ |grep jitsi <br>
drwxrwxr-x  14 bayu bayu 4,0K Apr 30 07:18 docker-jitsi-meet <br>
drwxrwxr-x   9 bayu bayu 4,0K Apr 30 07:19 .jitsi-meet-cfg <br>
<br>
nano .env <br>
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
<br>
#--- To stop command below, using "ctrl+c" <br>
docker-compose up <br>
<br>
#--- To stop command below (as daemon), using "docker-compose down" <br>
docker-compose up -d <br>
<br>
docker-compose ps -a <br>
           Name               Command   State                        Ports                        <br>
------------------------------------------------------------------------------------------------  <br>
docker-jitsi-meet_jicofo_1    /init     Up                                                        <br>
docker-jitsi-meet_jvb_1       /init     Up      0.0.0.0:10000->10000/udp, 0.0.0.0:4443->4443/tcp  <br>
docker-jitsi-meet_prosody_1   /init     Up      5222/tcp, 5269/tcp, 5280/tcp, 5347/tcp            <br>
docker-jitsi-meet_web_1       /init     Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp          <br>
<br>



# Notes :
<br>
- If you edit file ".env" then you must remove ~/.jitsi-meet-cfg then recreate ~/.jitsi-meet-cfg <br> 
<br>
- Backup Source Code & Config Jitsi-Meet Application using this command <br> 
<br>
sudo tar -czvf backup_20200508_docker-jitsi-meet.tar.gz docker-jitsi-meet/ <br>
docker-jitsi-meet/ <br>
docker-jitsi-meet/web/ <br>
docker-jitsi-meet/web/Makefile <br>
docker-jitsi-meet/web/rootfs/ <br>
docker-jitsi-meet/web/rootfs/etc/ <br>
docker-jitsi-meet/web/rootfs/etc/services.d/ <br>
docker-jitsi-meet/web/rootfs/etc/services.d/cron/ <br>
docker-jitsi-meet/web/rootfs/etc/services.d/cron/run <br>
docker-jitsi-meet/web/rootfs/etc/services.d/nginx/ <br>
docker-jitsi-meet/web/rootfs/etc/services.d/nginx/run <br>
docker-jitsi-meet/web/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/web/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/web/rootfs/defaults/ <br>
docker-jitsi-meet/web/rootfs/defaults/ssl.conf <br>
docker-jitsi-meet/web/rootfs/defaults/meet.conf <br>
docker-jitsi-meet/web/rootfs/defaults/letsencrypt-renew <br>
docker-jitsi-meet/web/rootfs/defaults/nginx.conf <br>
docker-jitsi-meet/web/rootfs/defaults/default <br>
docker-jitsi-meet/web/Dockerfile <br>
docker-jitsi-meet/jigasi/ <br>
docker-jitsi-meet/jigasi/Makefile <br>
docker-jitsi-meet/jigasi/rootfs/ <br>
docker-jitsi-meet/jigasi/rootfs/etc/ <br>
docker-jitsi-meet/jigasi/rootfs/etc/services.d/ <br>
docker-jitsi-meet/jigasi/rootfs/etc/services.d/jigasi/ <br>
docker-jitsi-meet/jigasi/rootfs/etc/services.d/jigasi/run <br>
docker-jitsi-meet/jigasi/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/jigasi/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/jigasi/rootfs/defaults/ <br>
docker-jitsi-meet/jigasi/rootfs/defaults/sip-communicator.properties <br>
docker-jitsi-meet/jigasi/rootfs/defaults/logging.properties <br>
docker-jitsi-meet/jigasi/Dockerfile <br>
docker-jitsi-meet/.env.bak <br>
docker-jitsi-meet/README.md <br>
docker-jitsi-meet/base-java/ <br>
docker-jitsi-meet/base-java/Makefile <br>
docker-jitsi-meet/base-java/Dockerfile <br>
docker-jitsi-meet/etherpad/ <br>
docker-jitsi-meet/etherpad/Makefile <br>
docker-jitsi-meet/etherpad/rootfs/ <br>
docker-jitsi-meet/etherpad/rootfs/defaults/ <br>
docker-jitsi-meet/etherpad/rootfs/defaults/settings.json <br>
docker-jitsi-meet/etherpad/Dockerfile <br>
docker-jitsi-meet/jicofo/ <br>
docker-jitsi-meet/jicofo/Makefile <br>
docker-jitsi-meet/jicofo/rootfs/ <br>
docker-jitsi-meet/jicofo/rootfs/etc/ <br>
docker-jitsi-meet/jicofo/rootfs/etc/services.d/ <br>
docker-jitsi-meet/jicofo/rootfs/etc/services.d/jicofo/ <br>
docker-jitsi-meet/jicofo/rootfs/etc/services.d/jicofo/run <br>
docker-jitsi-meet/jicofo/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/jicofo/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/jicofo/rootfs/defaults/ <br>
docker-jitsi-meet/jicofo/rootfs/defaults/sip-communicator.properties <br>
docker-jitsi-meet/jicofo/rootfs/defaults/logging.properties <br>
docker-jitsi-meet/jicofo/Dockerfile <br>
docker-jitsi-meet/CHANGELOG.md <br>
docker-jitsi-meet/gen-passwords.sh <br>
docker-jitsi-meet/.gitignore <br>
docker-jitsi-meet/jigasi.yml <br>
docker-jitsi-meet/base/ <br>
docker-jitsi-meet/base/Makefile <br>
docker-jitsi-meet/base/rootfs/ <br>
docker-jitsi-meet/base/rootfs/etc/ <br>
docker-jitsi-meet/base/rootfs/etc/services.d/ <br>
docker-jitsi-meet/base/rootfs/etc/services.d/.gitkeep <br>
docker-jitsi-meet/base/rootfs/etc/apt/ <br>
docker-jitsi-meet/base/rootfs/etc/apt/apt.conf.d/ <br>
docker-jitsi-meet/base/rootfs/etc/apt/apt.conf.d/99local <br>
docker-jitsi-meet/base/rootfs/etc/fix-attrs.d/ <br>
docker-jitsi-meet/base/rootfs/etc/fix-attrs.d/.gitkeep <br>
docker-jitsi-meet/base/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/base/rootfs/etc/cont-init.d/.gitkeep <br>
docker-jitsi-meet/base/rootfs/etc/cont-init.d/01-set-timezone <br>
docker-jitsi-meet/base/rootfs/etc/cont-finish.d/ <br>
docker-jitsi-meet/base/rootfs/etc/cont-finish.d/.gitkeep <br>
docker-jitsi-meet/base/rootfs/usr/ <br>
docker-jitsi-meet/base/rootfs/usr/bin/ <br>
docker-jitsi-meet/base/rootfs/usr/bin/apt-cleanup <br>
docker-jitsi-meet/base/rootfs/usr/bin/tpl <br>
docker-jitsi-meet/base/rootfs/usr/bin/apt-dpkg-wrap <br>
docker-jitsi-meet/base/Dockerfile <br>
docker-jitsi-meet/docker-compose.yml <br>
docker-jitsi-meet/Makefile <br>
docker-jitsi-meet/resources/ <br>
docker-jitsi-meet/resources/docker-jitsi-meet.png <br>
docker-jitsi-meet/resources/jitsi-docker.png <br>
docker-jitsi-meet/resources/docker-jitsi-meet.xml <br>
docker-jitsi-meet/jvb/ <br>
docker-jitsi-meet/jvb/Makefile <br>
docker-jitsi-meet/jvb/rootfs/ <br>
docker-jitsi-meet/jvb/rootfs/etc/ <br>
docker-jitsi-meet/jvb/rootfs/etc/services.d/ <br>
docker-jitsi-meet/jvb/rootfs/etc/services.d/jvb/ <br>
docker-jitsi-meet/jvb/rootfs/etc/services.d/jvb/run <br>
docker-jitsi-meet/jvb/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/jvb/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/jvb/rootfs/defaults/ <br>
docker-jitsi-meet/jvb/rootfs/defaults/sip-communicator.properties <br>
docker-jitsi-meet/jvb/rootfs/defaults/logging.properties <br>
docker-jitsi-meet/jvb/Dockerfile <br>
docker-jitsi-meet/.git/ <br>
docker-jitsi-meet/.git/index <br>
docker-jitsi-meet/.git/config <br>
docker-jitsi-meet/.git/logs/ <br>
docker-jitsi-meet/.git/logs/HEAD <br>
docker-jitsi-meet/.git/logs/refs/ <br>
docker-jitsi-meet/.git/logs/refs/remotes/ <br>
docker-jitsi-meet/.git/logs/refs/remotes/origin/ <br>
docker-jitsi-meet/.git/logs/refs/remotes/origin/HEAD <br>
docker-jitsi-meet/.git/logs/refs/heads/ <br>
docker-jitsi-meet/.git/logs/refs/heads/master <br>
docker-jitsi-meet/.git/info/ <br>
docker-jitsi-meet/.git/info/exclude <br>
docker-jitsi-meet/.git/hooks/ <br>
docker-jitsi-meet/.git/hooks/commit-msg.sample <br>
docker-jitsi-meet/.git/hooks/pre-rebase.sample <br>
docker-jitsi-meet/.git/hooks/post-update.sample <br>
docker-jitsi-meet/.git/hooks/prepare-commit-msg.sample <br>
docker-jitsi-meet/.git/hooks/pre-applypatch.sample <br>
docker-jitsi-meet/.git/hooks/update.sample <br>
docker-jitsi-meet/.git/hooks/pre-commit.sample <br>
docker-jitsi-meet/.git/hooks/pre-push.sample <br>
docker-jitsi-meet/.git/hooks/pre-push.sample <br>
docker-jitsi-meet/.git/hooks/applypatch-msg.sample <br>
docker-jitsi-meet/.git/branches/ <br>
docker-jitsi-meet/.git/objects/ <br>
docker-jitsi-meet/.git/objects/info/ <br>
docker-jitsi-meet/.git/objects/pack/ <br>
docker-jitsi-meet/.git/objects/pack/pack-9bcad6a95d237ba1f9246ce08f6acb00a987280f.idx <br>
docker-jitsi-meet/.git/objects/pack/pack-9bcad6a95d237ba1f9246ce08f6acb00a987280f.pack <br>
docker-jitsi-meet/.git/HEAD <br>
docker-jitsi-meet/.git/refs/ <br>
docker-jitsi-meet/.git/refs/tags/ <br>
docker-jitsi-meet/.git/refs/remotes/ <br>
docker-jitsi-meet/.git/refs/remotes/origin/ <br>
docker-jitsi-meet/.git/refs/remotes/origin/HEAD <br>
docker-jitsi-meet/.git/refs/heads/ <br>
docker-jitsi-meet/.git/refs/heads/master <br>
docker-jitsi-meet/.git/description <br>
docker-jitsi-meet/.git/packed-refs <br>
docker-jitsi-meet/jibri/ <br>
docker-jitsi-meet/jibri/Makefile <br>
docker-jitsi-meet/jibri/rootfs/ <br>
docker-jitsi-meet/jibri/rootfs/etc/ <br>
docker-jitsi-meet/jibri/rootfs/etc/opt/ <br>
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/ <br>
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/ <br>
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/managed/ <br>
docker-jitsi-meet/jibri/rootfs/etc/opt/chrome/policies/managed/managed_policies.json <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/ <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/20-icewm/ <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/20-icewm/run <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/10-xorg/ <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/10-xorg/run <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/30-jibri/ <br>
docker-jitsi-meet/jibri/rootfs/etc/services.d/30-jibri/run <br>
docker-jitsi-meet/jibri/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/jibri/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/jibri/rootfs/defaults/ <br>
docker-jitsi-meet/jibri/rootfs/defaults/config.json <br>
docker-jitsi-meet/jibri/rootfs/defaults/logging.properties <br>
docker-jitsi-meet/jibri/Dockerfile <br>
docker-jitsi-meet/.env <br>
docker-jitsi-meet/LICENSE <br>
docker-jitsi-meet/jibri.yml <br>
docker-jitsi-meet/prosody/ <br>
docker-jitsi-meet/prosody/Makefile <br>
docker-jitsi-meet/prosody/rootfs/ <br>
docker-jitsi-meet/prosody/rootfs/etc/ <br>
docker-jitsi-meet/prosody/rootfs/etc/services.d/ <br>
docker-jitsi-meet/prosody/rootfs/etc/services.d/10-saslauthd/ <br>
docker-jitsi-meet/prosody/rootfs/etc/services.d/10-saslauthd/run <br>
docker-jitsi-meet/prosody/rootfs/etc/services.d/prosody/ <br>
docker-jitsi-meet/prosody/rootfs/etc/services.d/prosody/run <br>
docker-jitsi-meet/prosody/rootfs/etc/sasl/ <br>
docker-jitsi-meet/prosody/rootfs/etc/sasl/xmpp.conf <br>
docker-jitsi-meet/prosody/rootfs/etc/cont-init.d/ <br>
docker-jitsi-meet/prosody/rootfs/etc/cont-init.d/10-config <br>
docker-jitsi-meet/prosody/rootfs/defaults/ <br>
docker-jitsi-meet/prosody/rootfs/defaults/conf.d/ <br>
docker-jitsi-meet/prosody/rootfs/defaults/conf.d/jitsi-meet.cfg.lua <br>
docker-jitsi-meet/prosody/rootfs/defaults/saslauthd.conf <br>
docker-jitsi-meet/prosody/rootfs/defaults/prosody.cfg.lua <br>
docker-jitsi-meet/prosody/Dockerfile <br>
docker-jitsi-meet/examples/ <br>
docker-jitsi-meet/examples/README.md <br>
docker-jitsi-meet/examples/traefik/ <br>
docker-jitsi-meet/examples/traefik/README.md <br>
docker-jitsi-meet/examples/traefik/docker-compose.yml <br>
docker-jitsi-meet/examples/kubernetes/ <br>
docker-jitsi-meet/examples/kubernetes/README.md <br>
docker-jitsi-meet/examples/kubernetes/deployment.yaml <br>
docker-jitsi-meet/examples/kubernetes/web-service.yaml <br>
docker-jitsi-meet/examples/kubernetes/jvb-service.yaml <br>
docker-jitsi-meet/examples/traefik-v2/ <br>
docker-jitsi-meet/examples/traefik-v2/README.md <br>
docker-jitsi-meet/examples/traefik-v2/docker-compose.yml <br>
docker-jitsi-meet/env.example <br>
docker-jitsi-meet/etherpad.yml <br>
<br>
<br>
sudo tar -czvf backup_20200508_jitsi-meet-cfg.tar.gz .jitsi-meet-cfg/ <br>
.jitsi-meet-cfg/ <br>
.jitsi-meet-cfg/web/ <br>
.jitsi-meet-cfg/web/le-renew.log <br>
.jitsi-meet-cfg/web/keys/ <br>
.jitsi-meet-cfg/web/interface_config.js <br>
.jitsi-meet-cfg/web/letsencrypt/ <br>
.jitsi-meet-cfg/web/letsencrypt/csr/ <br>
.jitsi-meet-cfg/web/letsencrypt/csr/0000_csr-certbot.pem <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/ <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/ <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/ <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/ <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/private_key.json <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/meta.json <br>
.jitsi-meet-cfg/web/letsencrypt/accounts/acme-v02.api.letsencrypt.org/directory/375d7a4a204869fe7db094935a2517a8/regr.json <br>
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/ <br>
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/deploy/ <br>
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/post/ <br>
.jitsi-meet-cfg/web/letsencrypt/renewal-hooks/pre/ <br>
.jitsi-meet-cfg/web/letsencrypt/keys/ <br>
.jitsi-meet-cfg/web/letsencrypt/keys/0000_key-certbot.pem <br>
.jitsi-meet-cfg/web/letsencrypt/archive/ <br>
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/ <br>
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/fullchain1.pem <br>
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/chain1.pem <br>
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/cert1.pem <br>
.jitsi-meet-cfg/web/letsencrypt/archive/vpsmeet.idjvnix.com/privkey1.pem <br>
.jitsi-meet-cfg/web/letsencrypt/renewal/ <br>
.jitsi-meet-cfg/web/letsencrypt/renewal/vpsmeet.idjvnix.com.conf <br>
.jitsi-meet-cfg/web/letsencrypt/live/ <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/ <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/fullchain.pem <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/cert.pem <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/chain.pem <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/README <br>
.jitsi-meet-cfg/web/letsencrypt/live/vpsmeet.idjvnix.com/privkey.pem <br>
.jitsi-meet-cfg/web/letsencrypt/live/README <br>
.jitsi-meet-cfg/web/config.js <br>
.jitsi-meet-cfg/web/nginx/ <br>
.jitsi-meet-cfg/web/nginx/ssl.conf <br>
.jitsi-meet-cfg/web/nginx/meet.conf <br>
.jitsi-meet-cfg/web/nginx/nginx.conf <br>
.jitsi-meet-cfg/web/nginx/site-confs/ <br>
.jitsi-meet-cfg/web/nginx/site-confs/default <br>
.jitsi-meet-cfg/jigasi/ <br>
.jitsi-meet-cfg/transcripts/ <br>
.jitsi-meet-cfg/jicofo/ <br>
.jitsi-meet-cfg/jicofo/sip-communicator.properties <br>
.jitsi-meet-cfg/jicofo/logging.properties <br>
.jitsi-meet-cfg/jvb/ <br>
.jitsi-meet-cfg/jvb/sip-communicator.properties <br>
.jitsi-meet-cfg/jvb/logging.properties <br>
.jitsi-meet-cfg/jibri/ <br>
.jitsi-meet-cfg/prosody/ <br>
.jitsi-meet-cfg/prosody/config/ <br>
.jitsi-meet-cfg/prosody/config/data/ <br>
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/ <br>
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/accounts/ <br>
.jitsi-meet-cfg/prosody/config/data/recorder%2emeet%2ejitsi/accounts/recorder.dat <br>
.jitsi-meet-cfg/prosody/config/data/.rnd <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/ <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/ <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jibri.dat <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jvb.dat <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/jigasi.dat <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/accounts/focus.dat <br>
.jitsi-meet-cfg/prosody/config/data/auth%2emeet%2ejitsi/offline/ <br>
.jitsi-meet-cfg/prosody/config/conf.d/ <br>
.jitsi-meet-cfg/prosody/config/conf.d/jitsi-meet.cfg.lua <br>
.jitsi-meet-cfg/prosody/config/saslauthd.conf <br>
.jitsi-meet-cfg/prosody/config/certs/ <br>
.jitsi-meet-cfg/prosody/config/certs/auth.meet.jitsi.key <br>
.jitsi-meet-cfg/prosody/config/certs/meet.jitsi.crt <br>
.jitsi-meet-cfg/prosody/config/certs/meet.jitsi.key <br>
.jitsi-meet-cfg/prosody/config/certs/auth.meet.jitsi.crt <br>
.jitsi-meet-cfg/prosody/config/prosody.cfg.lua <br>
.jitsi-meet-cfg/prosody/prosody-plugins-custom/ <br>
<br>


# Refference :
https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md <br>
https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md <br>
https://github.com/jitsi/docker-jitsi-meet/blob/master/README.md <br>
<br>
