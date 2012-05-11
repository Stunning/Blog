Databases and backup
====================

This is how to create separate databases with their own users, and then setup backup to GIT.


Database configuration
----------------------

Login as root::

	mysql --user=root --password='<root password>'

Create user ``foo`` for the database ``bar``:

.. code-block:: sql

	CREATE DATABASE bar;
	GRANT ALL ON bar.* TO 'foo'@'localhost' IDENTIFIED BY '<foo password>';

Create the ``backup`` user:

.. code-block:: sql

	GRANT SELECT, LOCK TABLES ON *.* TO 'backup'@'localhost' IDENTIFIED BY '<backup password>';



Create a backup cron job
------------------------

Setup a remote GIT repository. We use Assembla. To setup your own, see :ref:`version_own_remote`. 
Use an account dedicated to these kinds of shared operations. We use ``stunning``. 

Create a directory in the user home dir::

	mkdir backup_db
	cd backup_db

Setup the local repository according to :ref:`version_local`.
	
Create the backup script::

	nano backup.sh
	
with the contents:

.. code-block:: bash

	#!/bin/bash -xe
	USER='backup'
	PASS='<backup password>'
	CRED="--user=$USER --password=$PASS"
	DBS=$(
		mysql $CRED --batch --skip-column-names --execute='show databases' |
		grep -F -v -e 'mysql' -e 'information_schema'
	)
	for DB in $DBS; do
		FILE=$DB.sql
		mysqldump $CRED --compact --database $DB > $FILE
		git add $FILE
	done
	git add $0
	git commit -m "Automatic backup"
	git push origin master

Make the script executable::

	chmod +x backup.sh

Test it::

	./backup.sh
	
Edit the user's crontab::

	crontab -e

Add the line::

	0 0 * * * (cd backup-db && ./backup.sh >> log 2>&1)

It will be executed at midnight.


Restoring from backup
---------------------

To restore a backup to the database ``bar`` above::

	mysql --user=foo --password='<foo password>' bar < all-databases.sql
