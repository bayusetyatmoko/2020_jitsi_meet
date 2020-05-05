# 2020_jitsi_meet
How To Jitsi Meet Instalation (Surabaya, 2020-05-05)

# Case 01 - Install Jitsi Meet on O/S Centos 7 (via docker) 
# a. Prepare Centos Repo & App
sudo yum check-update <br>
sudo yum install nano python3 curl elinks <br>
sudo yum install git <br>

# b. Prepare Hosts & Hostname
sudo nano /etc/hosts <br>
127.0.0.1	localhost <br>
--- change with your configuration <br>
139.99.90.116 	    vpsmeet.idjvnix.com      vpsmeet <br>







# Refference :
https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md
https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md
https://github.com/jitsi/docker-jitsi-meet/blob/master/README.md
