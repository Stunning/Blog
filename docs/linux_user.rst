Linux User Handling
===================

This is a short memo on how to create and login as users.

Login as root.

Create the user *foobar*::

	adduser foobar
	
Add the webserver user *www-data* to foobar's group::

	adduser www-data foobar
	
Login as foobar.

To be able to login without password:

#) On your local box, copy your public key - the contents of ~/.ssh/id_rsa.pub. I you don't have it, 
   create it with ssh-keygen. Leave the defaults. 
#) Logged in as foobar, create or append to ~/.ssh/authorized_keys. Paste the public key. Logout. 
   Next time you log in as foobar, you will automatically use your private key to prove that you are 
   the owner of the entry in foobar's authorized_keys.
  
Create files to be served by the web server::

	mkdir public_html
	cd public_html
	mkdir index.html
	
New files are created with the foobar User and Group. By default umask setting, only User has write access, and everyone has read access::

	umask -S

Gives ``u=rwx,g=rx,o=rx``. The format is User/Group/Others=Read/Write/eXecute. You can also use ``a`` for **All**. 

To give group members write access:: 

	umask g+w
	
That will give the webserver write access to any new files we create.

To change access for existing file trees, see these examples::

	chmod -R g+w media/
	chmod -R g-w js/
	
You can check the permissions with::

	ls -lh index.html
	ls -lhd media/
	ls -lh media/
	
The format is ``<directory?><perm User><perm Group><perm Others> <hard link count> <User> <group> <size> <modified> <name>``

You can also change the user/group with::

	chown -R newuser:newgroup /tmp/somefile
	

GIT
---

To be able to pull from assembla, create ssh keys like above but for this user. Paste the public key in the settings of a read-only account in Assembla.

Alternatively, copy the .ssh/{id_rsa,id_rsa.pub} from another account that already has an Assembla user, to this user. One such account is halebop musik at Webbe

Then you can just initially::

	git clone git@git.assembla.com:stunning-mtg.git
	
And subsequently::

	cd stunning-mtg
	git pull


Some useful commands
--------------------

* ``pwd``: Print working directory
* ``nano <file>``: Terminal text editor
* ``top``: Running processes. Type *M* to sort by memory usage
* ``pgrep -fl '.*my_virus.*'``: Search for process by regex
* ``ln -s ../css/base.css my_base_css_softlink``: Create softlinks
* ``sudo <command>``: Run as root

	