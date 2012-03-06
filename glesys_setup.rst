apt-get update
apt-get install ncurses-term
apt-get install php5-mysql php5-mcrypt php5-gd
apt-get install mysql-server
apt-get install libapache2-mod-php5
apt-get install apache2
apt-get install python-imaging
apt-get install lsof
apt-get install python-pip

customize /etc/nanorc

adduser --system www-wsgi --group=www-wsgi
adduser www-wsgi www-data

cd /etc/apache2/cert
openssl genrsa 2048 > stunning.se-key.pem
openssl req -new -x509 -key stunning.se-key.pem -out stunning.se-self-cert.pem -days 3650
or
openssl req -new -key stunning.se-key.pem -out stunning.se-startcom-request.csr
