# 2020_jitsi_meet
How To Jitsi Meet Instalation (Surabaya, 2020-05-05)

# Case 1 - Install Jitsi Meet on O/S Centos 7 (via docker) 
# 1.01. Prepare Centos Repo & App
sudo yum check-update <br>
sudo yum install nano python3 curl elinks <br>
sudo yum install git <br>
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

# 1.03. Prepare Timezone, Date & NTP
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



# Refference :
https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md <br>
https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md <br>
https://github.com/jitsi/docker-jitsi-meet/blob/master/README.md <br>
<br>
