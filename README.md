# Nagios Full Installations 

<pre>
sudo apt update && apt upgrade -y  
</pre>
<pre>
sudo apt install autoconf gcc libc6 make wget unzip apache2 apache2-utils php libgd-dev libmcrypt-dev libssl-dev bc gawk dc build-essential snmp libnet-snmp-perl gettext
</pre>
<pre>
sudo useradd -m -s /bin/bash nagios
sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
sudo usermod -a -G nagcmd www-data
</pre>
<pre>
  cd /tmp
  wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.5.9.tar.gz ./
  
</pre>
<pre>
  tar -zxvf nagios-4.5.9.tar.gz
  mv /opt/nagios-4.5.9
  cd /opt/nagios-4.5.9/
</pre>
## Nagios in ubuntu and compile it 
<pre>
./configure --with-nagios-group=nagios --with-command-group=nagcmd
make all
</pre>
## Install Nagios Core Binaries and Web Interface Files
<pre>
sudo make install
sudo make install-commandmode
sudo make install-init
sudo make install-config
sudo /usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-available/nagios.conf
</pre>
## Install Nagios on Ubuntu Plugins
<pre>
cd /tmp
wget http://www.nagios-plugins.org/download/nagios-plugins-2.4.9.tar.gz
mv nagios-plugins-2.4.9 /opt/
cd nagios-plugins-2.4.9/
sudo ./configure --with-nagios-user=nagios --with-nagios-group=nagcmd --with-openssl
make install
</pre>

##  Nagios configuration and restart Nagios
<pre>
sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
sudo systemctl restart nagios
sudo systemctl restart apache2
</pre>
## Configure Apache Web Server
<pre>
  sudo ln -s /etc/apache2/sites-available/nagios.conf /etc/apache2/sites-enabled/
sudo a2enmod rewrite
sudo a2enmod cgi
</pre>
## Set admin Password
<pre>
  sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
</pre>
## Start Nagios and Apache Services
<pre>
sudo systemctl enable nagios
sudo systemctl enable apache2
sudo systemctl restart nagios
sudo systemctl restart apache2
</pre>
## Logging with user password
<pre>
http://192.168.122.61/nagios/  
</pre>













