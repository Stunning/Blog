Glesys Setup
============

* Install packages::

	apt-get update
	apt-get upgrade
	apt-get install ncurses-term
	apt-get install php5-mysql php5-mcrypt php5-gd
	apt-get install mysql-server
	apt-get install libapache2-mod-php5
	apt-get install libapache2-mod-wsgi
	apt-get install apache2
	apt-get install python-imaging
	apt-get install lsof
	apt-get install python-pip

* Set timezone::

	echo Europe/Stockholm > /etc/timezone 

* customize /etc/nanorc::
	
	set autoindent
	set tabsize 4

* set default language in /etc/bash.bashrc::

	export LANG=en_US
