Apache configuration
====================

This will enable serving several domain names, and several websites under each 
domain, with and without TLS. More domains can be added for more IPs. 
 
Setup apache
------------

Login as ``root``.

Generate TLS key::

	cd /etc/apache2/cert
	openssl genrsa 2048 > stunning.se-key.pem
	chgrp www-data stunning.se-key.pem
	chmod o-rwx stunning.se-key.pem

If this key is owned by a group that already has ``www-data`` as member, omit the ``chgrp`` command.

Either create a self-signed cert::

	openssl req -new -x509 -key stunning.se-key.pem -out stunning.se-self-cert.pem -days 3650

or create a request to get a cert from a Certificate Authority::

	openssl req -new -key stunning.se-key.pem -out stunning.se-startcom-request.csr

Enable the TLS module::

	ln -s ../mods-available/ssl.* /etc/apache2/mods-enabled/
	
Create the WSGI (django) user::

	adduser --system www-wsgi
	adduser www-wsgi www-data


Add a web site
--------------

create a softlink to the conf::

	ln -s /home/stunning-apps-com/apache.conf /etc/apache2/sites-enabled/stunning-apps-com

Create a site user::

	adduser stunning-apps-com
	adduser www-data stunning-apps-com

Create a sub site user::

	adduser purplepeople
	adduser www-data purplepeople

Login as ``stunning-apps-com``. You are now in ``/home/stunning-apps-com``.

Edit apache.conf::

	NameVirtualHost 109.74.6.41:443
	<VirtualHost 109.74.6.41:443>
   	SSLCertificateFile      /etc/apache2/cert/www.stunning-apps.com.crt
   	SSLCertificateKeyFile   /etc/apache2/cert/stunning-apps.com-key.pem
   	SSLCertificateChainFile /etc/apache2/cert/www.stunning-apps.com.intermediate.crt
   	SSLEngine on
	</VirtualHost>
	
	<VirtualHost 109.74.6.41:80 109.74.6.41:443>
	
   	ServerName www.stunning-apps.com
   	
   	LogLevel warn
   	ErrorLog /var/log/apache2/stunning-apps-com-error.log
   	
   	Alias /robots.txt  /home/stunning-apps-com/public_html/robots.txt
   	Alias /favicon.ico /home/stunning-apps-com/public_html/favicon.ico
   	<Directory /home/stunning-apps-com/public_html>
   	Order deny,allow
   	Allow from all
   	</Directory>
   	
   	### Include all sub sites. The must contain an alias such as:
   	### Alias /purplepeople /home/purplepeople/public_html
   	Include /home/purplepeople/apache-sub.conf
	
	</VirtualHost>
	
Optionally deploy ``public_html/robots.txt`` and ``public_html/favicon.ico``.

Login as ``purplepeople``. You are now in ``/home/purplepeople``.

Edit apache-sub.conf::

	Alias /purplepeople /home/purplepeople/public_html
	<Directory /home/purplepeople/public_html>
	Order deny,allow
	Allow from all
	</Directory>

Deploy the served contents under ``public_html/``.

Login as ``root``, and restart apache::

	invoke-rc.d apache2 restart

   
Make a domain canonical
-----------------------

A common practice in Search Engine Optimization (SEO) is to collect all Page Rank to one canonical domain/URL.

For instance, we have to choose whether to use the www subdomain. If we don't, 
we need to redirect it to the main domain. If we do, we need to redirect the 
main domain to to the www subdomain.

This can be done using ``RedirectPermanent`` in the ``VirtualHost`` of non-canonical 
domains. Assume we have the previous setup:: 

   NameVirtualHost 109.74.6.41:80

   <VirtualHost 109.74.6.41:80>
      ServerName www.stunning-apps.com
      ...
   </VirtualHost>
   
Now add the non-canonical ``VirtualHost``::

   <VirtualHost 109.74.6.41:80>
      ServerName stunning-apps.com
      RedirectPermanent / http://www.stunning-apps.com/
   </VirtualHost>

This will permanently redirect any URL path that starts with the prefix 
``/`` (which all URL paths do) to the canonical domain. The rest of the URL path after the 
``/`` prefix will be added to the target URL prefix 
``http://www.stunning-apps.com/``. 


Collect statistics
------------------

You can use awstats_ to collect statistics from the access log. View it on stunning-apps_.

Install awstats::

   apt-get install awstats

You find the debian documentation here::

   gunzip < /usr/share/doc/awstats/README.Debian.gz | less

Edit ``/etc/awstats/awstats.conf.local``::

   LogFile="/var/log/apache2/other_vhosts_access.log"
   LogFormat="%virtualname %host %other %logname %time1 %methodurl %code %bytesd %refererquot %uaquot"
   SiteDomain="awstats.stunning-apps.com"
   HostAliases="REGEX[.*]"
   DNSLookup=0
   AllowToUpdateStatsFromBrowser=1
   
   ExtraSectionName1="Virtual Hosts"
   ExtraSectionCodeFilter1="200 304"
   ExtraSectionFirstColumnTitle1="Virtual Host"
   ExtraSectionFirstColumnValues1="VHOST,(.*)"
   ExtraSectionFirstColumnFormat1="%s"
   ExtraSectionStatTypes1=UVPHB
   ExtraSectionAddAverageRow1=0
   ExtraSectionAddSumRow1=1
   MaxNbOfExtra1=20
   MinHitExtra1=1

Create a virtual host for awstats in ``/etc/apache2/sites-available/awstats``::

   <VirtualHost 109.74.6.41:80>
        ServerName awstats.stunning-apps.com
        Include /usr/share/doc/awstats/examples/apache.conf
   </VirtualHost>

Enable it by softlinking ``/etc/apache2/sites-enabled/awstats -> ../sites-available/awstats``.

Make logfiles readable by ``www-data``::

   chmod o+x /var/log/apache2
   chmod o+r /var/log/apache2/*.log

Config **logrotate** to make log files readeble, and to update statistics data.
In ``/etc/logrotate.d/apache2``, edit ``create`` line permission to read::

   create 644 root adm

And add this ``prerotate``::

   prerotate
   if [ -x /usr/share/awstats/tools/update.sh ]; then
      su - -c /usr/share/awstats/tools/update.sh www-data
   fi
   endscript

So that the result becomes::

   /var/log/apache2/*.log {
      weekly
      missingok
      rotate 52
      compress
      delaycompress
      notifempty
      create 644 root adm
      sharedscripts
      prerotate
      if [ -x /usr/share/awstats/tools/update.sh ]; then
         su - -c /usr/share/awstats/tools/update.sh www-data
      fi
      endscript
      postrotate
         /etc/init.d/apache2 reload > /dev/null
      endscript
   }


.. _awstats: http://awstats.sourceforge.net/
.. _stunning-apps: http://awstats.stunning-apps.com/cgi-bin/awstats.pl 