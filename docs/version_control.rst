Version control
===============


Setup a local repository
------------------------

You need to add your SSH public key to the remote repository. 
In this example we use Assembla_.
If you have one in ~/.ssh/id_rsa.pub, use that one. 
If not, create it with ``ssh-keygen``. Leave the defaults.

Git comes with both a command line interface (cli) - ``git``, and a graphical user interface (gui) - ``git-gui``. 
They and other gui clients can be downloaded from <http://git-scm.com/downloads>. 
Eclipse can use the plugin egit. 

GIT cli
~~~~~~~

Learn the basics at `Git Reference`_. See also `GIT documentation`_.

If you haven't installed GIT::

	apt-get install git
	
If you haven't set up the GIT user (stored in ~/.gitconfig)::

	git config --global user.name <some name>
	git config --global user.email <some email>

Create a working directory::

	mkdir <working dir>
	cd <working dir>

Initialize the GIT repository::

	git init
	git remote add origin <repository url> 

Where <repository url> can be for instance: 

- ``git@git.assembla.com:<Assembla repository name>.git``
- ``git@stunning-apps.com:bilia/nostalgi.git``


Set up a your own remote repository
-----------------------------------

These instructions are based on `the Pro Git Book - Setting Up the Server`_.

The following assumes you are logged in as ``root``

Create the user ``git``::

	adduser --system --shell=/usr/bin/git-shell git

Initialse a bare repository. Bare means no working files::

	mkdir -p /home/git/bilia/nostalgi.git
	cd /home/git/bilia/nostalgi.git
	git --bare init
	chown -R git /home/git

To give access to a user, append their SSH public key to ``git``'s authorized keys::

	mkdir /home/git/.ssh
	cat >> /home/git/.ssh/authorized_keys

``cat`` will wait for input on standard input, so just paste the contents of the public key 
into the terminal, and finish with a newline and an end-of-file character - <enter> and <control-d>.


.. _GIT documentation: http://git-scm.com/doc 
.. _Assembla: https://www.assembla.com/
.. _Git Reference: http://gitref.org/index.html
.. _the Pro Git Book - Setting Up the Server: http://git-scm.com/book/en/Git-on-the-Server-Setting-Up-the-Server
