Integrated Development Environment
==================================

Eclipse
-------

At time of writing the latest Eclipse release is Juno - 4.2. It can be 
downloaded from `Eclipse Downloads`_. Click the *Latest Release*, and 
download the *Platform Runtime Binary* for your platform.

Extract the package where the user that will install plugins and update 
them has write access. I keep it in my user home.


Install plugins
***************

.. include:: <isonum.txt>

Start Eclipse, and go to **Help** |rarr| **Install New Software...**. 

If the update site is not listed in *Work with*, click **Add...**, 
and enter the url of the site below.

Site:

- Juno_ (or whatever version is current):
		
  - EGit
	
- PyDev_:
	
  - PyDev

- Google_:

  - Google Plugin for Eclipse 4.2
  - Google App Engine Java SDK
  
.. _Eclipse Downloads: http://download.eclipse.org/eclipse/downloads/
.. _Juno: http://download.eclipse.org/releases/juno
.. _PyDev: http://pydev.org/updates/
.. _Google: http://dl.google.com/eclipse/plugin/4.2