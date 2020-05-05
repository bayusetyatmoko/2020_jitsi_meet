# 2020_jitsi_meet
How To Jitsi Meet Instalation (Surabaya, 2020-05-05)

# Case 1 - Install Jitsi Meet on O/S Centos 7 (via docker) 
# 1.01. Prepare Centos Repo & App
sudo yum check-update <br>
sudo yum install nano python3 curl elinks <br>
sudo yum install git <br>

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



# Refference :
https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md <br>
https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md <br>
https://github.com/jitsi/docker-jitsi-meet/blob/master/README.md <br>
<br>
