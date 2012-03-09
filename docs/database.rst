Databases and backup
====================

This is how to create separate databases with their own users, and then setup backup to GIT.


Database configuration
----------------------

Login as root::

	mysql --user=root --password='<root password>'

Create user foo for the database bar::

	create database bar;
	create user foo@localhost identified by '<foo password>';
	grant all on bar.* to foo@localhost;

Create the backup user::

	create user backup@localhost identified by '<backup password>';
	grant select, lock tables on *.* to backup@localhost;
	exit


Create a backup cron job
------------------------

Setup a remote GIT repository. We use Assembla. Use an account dedicated to 
these kinds of shared operations. We use *stunning*. You need to add your SSH 
public key to Assembla. If you have one in ~/.ssh/id_rsa.pub, use that one. 
If not, create it with ``ssh-keygen``. Leave the defaults.

If you haven't installed GIT::

	apt-get install git
	
If you haven't set up the GIT user (stored in ~/.gitconfig)::

	git config --global user.name <some name>
	git config --global user.email <some email>

Create a directory in the user home dir::

	mkdir backup_db
	cd backup_db

Initialize the GIT repository::

	git init
	git remote add origin git@git.assembla.com:<Assembla repository name>.git
	
Create the backup script::

	nano backup.sh
	
with the contents::

	#!/bin/bash -x
	FILE=all-databases.sql
	mysqldump --user=backup --password='<backup password>' --all-databases > $FILE
	git add $FILE $0
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