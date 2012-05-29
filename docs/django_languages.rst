Multiple languages in Django
===============================

This is how to add Swedish translation to a django app called *myapp*.

Enter a Django app directory, and create the subdirectory ``locale``::

	cd myapp
	mkdir locale

Create the translation file ``locale/sv/LC_MESSAGES/django.po``:

	django-admin.py makemessages --locale=sv

Fill in all the translations in the .po file using a text editor or a tool like Virtaal_.

Compile the .po file into a .mo file with::

	django-admin.py compilemessages

To reparse the code for more translation strings in the future, use::

	django-admin.py makemessages -all


Creating translatable strings
-----------------------------

Wrap the text in functions of the ``django.utils.translation`` module, such as 
``ugettext`` or ``ugettext_lazy``. It is convenient and standard to alias the
function used to ``_``. For example:

.. code-block:: python

	from django.utils.translation import ugettext_lazy as _
	
	print "In your language, apple is called " + _("apple")


Unicode text in source files
----------------------------

To create unicode text strings, for instance with Swedish content, you need to 
define the encoding of the source file, and store it in a unicode string:

.. code-block:: python

	# -*- coding: utf-8 -*-
	
	text = u"En björn åt ett bär"



.. _Virtaal: http://translate.sourceforge.net/wiki/virtaal/index