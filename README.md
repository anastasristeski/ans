# ans
1
adduser ansxxxxx
groupadd ans
usermod -aG ans ansxxxxx
hostnamectl set-hostname ans-xxxxxx
(sudo on user) usermod -aG wheel ansxxxxx

2
dnf -y install bind bind-utils
	(named.conf)
	listen-on port 53{10.10.0.0/16; localhost;};
	allow-query {10.10.0.0/16; localhost;};
	systemctl enable --now named
	firewall-cmd --permanent --add-service=dns
	firewall-cmd --reload
	
	nano /etc/named.rfc1912.zones
	zone "xxxxxx.io.mk" IN {
		type master; // : 
		file "xxxxxx.io.mk.zone";	
};
	nano /var/named/xxxxxx.io.mk.zone
	or cp named.localhost 
	change permissions 
	chown root:named /var/named/INDEX.io.mk.zone
	chmod 640 /var/named/INDEX.io.mk.zone
	in .zone 
	$TTL 1d
	@   IN  SOA ns1.INDEX.io.mk. rname.invalid. (
        2026012901 ; serial
        1d         ; refresh
        1h         ; retry
        1w         ; expire
        3h )       ; minimum

	; Name servers
	@   IN  NS  ns1.INDEX.io.mk.
	@   IN  NS  ns.ans.mk.

	; A records
	ns1   IN  A   SERVER_IP
	www   IN  A   SERVER_IP
	ftp2  IN  A   SERVER_IP
	dns   IN  A   10.10.16.200

	; CNAME
	web   IN  CNAME www.INDEX.io.mk.

	named-checkzone 182065.io.mk /var/named/182065.io.mk.zone

	go to /etc
	options {
    directory "/var/named";

    listen-on port 53 { any; };
    allow-query { 10.10.0.0/16; localhost; };

    recursion yes;
    allow-recursion { 10.10.0.0/16; localhost; };

    forwarders {
        1.0.0.1;
        1.1.1.1;
    };

    forward only;
};

	named-checkconf
3.
dnf -y install httpd
	
systemctl enable --now httpd
sudo dnf update -y
sudo dnf install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload


firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
*opt(dnf -y install php php-cli php-common php-mysqlnd php-gd php-xml php-mbstring)
	REV PROXY NODE
	dnf -y install nodejs
	nano /etc/httpd/conf.d/node-proxy.conf
	<VirtualHost *:80>
    ServerName _

    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>


4.
ip -4 addr
dnf -y install httpd mariadb-server php php-cli php-common php-mysqlnd php-gd php-intl php-xml php-mbstring php-soap php-zip php-opcache php-curl wget tar
systemctl enable --now httpd mariadb
CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'StrongPass123!';
GRANT ALL PRIVILEGES ON moodle.* TO 'moodleuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
mysql -u moodleuser -p -e "SHOW DATABASES;"
cd /tmp
wget https://github.com/moodle/moodle/archive/refs/heads/MOODLE_501_STABLE.tar.gz -O moodle.tgz
tar -xzf moodle.tgz
rm -rf /var/www/moodle
mv moodle-MOODLE_501_STABLE /var/www/moodle
mkdir -p /var/moodledata
chown -R apache:apache /var/moodledata
chmod 770 /var/moodledata
chown -R apache:apache /var/www/moodle
dnf -y install policycoreutils-python-utils
semanage fcontext -a -t httpd_sys_rw_content_t "/var/moodledata(/.*)?"
semanage fcontext -a -t httpd_sys_content_t "/var/www/moodle(/.*)?"
restorecon -Rv /var/moodledata /var/www/moodle
getenforce
echo "Listen 8088" > /etc/httpd/conf.d/listen-8088.conf
nano /etc/httpd/conf/httpd.conf
ServerName 127.0.0.1
nano /etc/httpd/conf.d/moodle-8088.conf
<VirtualHost SERVER_IP:8088>
    ServerName SERVER_IP
    DocumentRoot /var/www/moodle

    <Directory /var/www/moodle>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog  /var/log/httpd/moodle_error.log
    CustomLog /var/log/httpd/moodle_access.log combined
</VirtualHost>
semanage port -a -t http_port_t -p tcp 8088
semanage port -m -t http_port_t -p tcp 8088
apachectl configtest

firewall-cmd --permanent --add-port=8088/tcp
firewall-cmd --reload
sudo nano /etc/resolv.conf

5.
sudo dnf install -y nfs-utils
sudo mkdir -p /nfs/jan2024
sudo mkdir -p /nfs/proba2024
sudo mount -t nfs 10.10.16.200:/mnt/kolokvium_2024 /nfs/jan2024
sudo mount -t nfs 10.10.16.200:/mnt/proba /nfs/proba2024
sudo nano /etc/fstab
10.10.16.200:/mnt/kolokvium_2024   /nfs/jan2024     nfs   defaults,_netdev   0 0
10.10.16.200:/mnt/proba           /nfs/proba2024   nfs   defaults,_netdev   0 0
cd /nfs/jan2024
sudo mkdir <index>
sudo touch /nfs/jan2024/<index>/test.txt
6.
sudo dnf install -y firewalld
sudo systemctl enable --now firewalld
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-service=ftp
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
sudo firewall-cmd --list-all

sudo systemctl stop firewalld
sudo systemctl disable firewalld

sudo dnf install -y epel-release
sudo dnf install -y ufw
sudo systemctl enable --now ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 53/tcp
sudo ufw allow 53/udp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 21/tcp
sudo ufw allow 8080/tcp
sudo ufw enable
sudo ufw status verbose



	

