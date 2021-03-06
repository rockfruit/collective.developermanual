============
PloneFormGen
============

.. admonition :: Description    

        PloneFormGen is a contentish editor for creating
        Plone forms through Plone edit interface.
        
Introduction
------------

* `PloneFormGen product page <http://plone.org/products/ploneformgen>`_

Creating content items from form submission
-------------------------------------------

* http://plone.org/products/ploneformgen/documentation/how-to/creating-content-from-pfg

Integrating custom field types
------------------------------

* http://svn.plone.org/svn/collective/Products.PloneFormGen/fields/PFGDataGrid/trunk/        

Fail-safe email sending
-------------------------

By default if SMTP server rejects the message send by PloneFormGen
the page will crash with an exception. Possible reasons for SMTP failure are

* SMTP server is down

* SMTP server is overloaded

* SMTP server spam protection is giving false positives for your email sending attempts 

If you have a situation where gathering the data is critical 
the following process is appropriate

* Use save data adapter to save results

* Use a custom email sender script adapter to send email and even if this
  step fails then the data is saved and no exception is given to the user
  
Example PloneFormGen script adapter (using proxy role Manager)::
   

	# -*- coding: utf-8 -*-
	from Products.CMFCore.utils import getToolByName

	# This script will send email to several recipients
	# each written down to its own email field
	# whose id starts with "email-"
	emails = []

	for key in fields:
	  if key.startswith('email-'):
	    if fields[key] != '':
	      emails.append(fields[key])


	mailhost = getToolByName(ploneformgen, 'MailHost')

	subject = "Huuhaa"

	# Custom message with a name filled in
	message = u"""Hello,
	
	Thanks for participating %s !
	Cheers,
	http://www.opensourcehacker.com
	""" % (fields['etunimi'])

	source = "info@opensourcehacker.com"

	for email in emails:
	  try:
	    mailhost.secureSend(message, email, source, subject=subject, subtype='plain', charset="utf-8", debug=False, From=source)
	  except Exception:
	    pass
		