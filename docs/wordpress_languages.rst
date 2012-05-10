Multiple languages in Wordpress
===============================

This will outline the steps for migrating a Swedish site to a multilingual site in both English and Swedish.

Install gettext as root::

	apt-get install gettext

Get the Wordpress i18n tools::

	cd ~
	svn co http://i18n.svn.wordpress.org/tools/trunk/ wordpress-i18n_tools
	cd wordpress-i18n_tools
	
Set the textdomain for all gettext entries::

	find ~/public_html/stunning-ml/wp-content/themes/stunning2 -regex '.*\.php' -exec php add-textdomain.php -i stunning '{}' \;

Collect translation strings::

	php makepot.php wp-theme ~/public_html/stunning-ml/wp-content/themes/stunning2 ~/public_html/stunning-ml/wp-content/themes/stunning2/stunning.pot

Go to the theme::

	cd ~/public_html/stunning-ml/wp-content/themes/stunning2

Merge the entries in ``stunning.pot`` into ``sv_SE.po`` without deleting the existing translations, 
and then translate any new entries. You can use tools such as Gtranslator_ or Virtaal_. You can also use the gettext tools.

.. _Gtranslator: http://projects.gnome.org/gtranslator/
.. _Virtaal: http://translate.sourceforge.net/wiki/virtaal/index


Using Virtaal
-------------

Open or create ``sv_SE.po`` in Virtaal. Choose **File** -> **Update To Template**, and select ``stunning.pot``. 
Edit the translations and save it. 
Finally export it to machine readable format by chooosing **File** -> **Export**, and select ``sv_SE.mo``. 


Using gettext
-------------

If ``sv_SE.po`` does not already exist::
 
	msginit -i stunning.pot -l sv_SE -o sv_SE.po
	
else::

	msgmerge -U sv_SE.po stunning.pot

and then directly edit the translations in ``sv_SE.po``.

Generate the machine readable files::

	msgfmt sv_SE.po -o sv_SE.mo
