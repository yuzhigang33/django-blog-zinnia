==========================
Frequently Asked Questions
==========================

.. contents::

.. _faq-templates:

Templates
=========

.. _customizing-templates:

The templates does not fit to my wishes. What can I do ?
--------------------------------------------------------

The templates provided for Zinnia are simple but complete and as generic as
possible. But you can easily change them by
`specifying a template directory`_. If you are not familiar with Django,
the part two of the excellent Django tutorial explains in detail
how to proceed for `customizing the look and feel`_ of the
:mod:`~django.contrib.admin` app, in Zinnia it's the same thing.

A good starting point is to copy-paste the :file:`zinnia/base.html` template,
and edit the :ttag:`extends` instruction for fitting to your skin.

.. note::
	* The main content is displayed in block named ``content``.
	* Additional datas are displayed in a block named ``sidebar``.

You can also create your own app containing some Zinnia's templates based
on inheritance. You can also create your own app containing some Zinnia’s
templates based on inheritance. For example you can find these two
applications which aim is to transform the templates for Zinnia to be HTML5
ready, which can be a good starting point to make your own at :

* `Zinnia-theme-html5`_.
* `Django Blog Quintet`_.

.. warning::
   .. versionchanged:: 0.9

   `Django Blog Quintet`_ is no longer compatible with Zinnia, but still be
   a good example.

.. _faq-comments:

Comments
========

.. _customizing-comments:

Is it possible to have a better comment system, with reply feature for example ?
--------------------------------------------------------------------------------

Yes the comment system integrated in Zinnia is based on
:mod:`django.contrib.comments` and can be extended or replaced.

If you want the ability to reply on comments, you can take a look at
`django-threadcomments`_ for example.

.. _faq-edition:

Edition
=======

.. _custom-markups:

I want to write my entries in `MarkDown`_, `RestructuredText`_ or any lightweight markup language, is it possible ?
-------------------------------------------------------------------------------------------------------------------

Yes of course, Zinnia currently support `MarkDown`_, `Textile`_ and
`reStructuredText`_ as markup languages, but if you want to write your
entries in a custom markup language a solution is to disable the WYSIWYG
editor in the admin site with the :setting:`ZINNIA_WYSIWYG` setting, and
use the appropriate template filter in your templates.

.. _faq-authors:

Authors
=======

.. _multiple-authors:

Is Zinnia able to allow multiple users to edit it's own blog ?
--------------------------------------------------------------

Zinnia is designed to be multi-site. That's mean you can publish entries on
several sites or share an admin interface for all the sites handled.

Zinnia also provides a new permission that's allow or not the user to
change the authors. Useful for collaborative works.

But if you want to restrict the edition of the entries by site, authors or
whatever you want, it's your job to implement this functionality in your
project.

The simple way to do that, respecting the Django rules, is to override the
admin classes provided by Zinnia, and register those classes in another
admin site.

.. _faq-images:

Images
======

.. _image-thumbnails:

How can I use the image field for fitting to my skin ?
------------------------------------------------------

Take a looks at `sorl.thumbnail`_ and use his templatetags.

You can do something like this in your templates :

.. code-block:: html+django

  <img src="{% thumbnail object.image 250x250 %}" />

.. _image-gallery:

I want an image gallery in my posts, what can I do ?
----------------------------------------------------

Simply create a new application with a model named :class:`EntryImage` with a
:class:`~django.db.models.ForeignKey` to the :class:`~zinnia.models.Entry`
model.

Then in the admin module of your app, unregister the
:class:`~zinnia.admin.entry.EntryAdmin` class, and use
:class:`~django.contrib.admin.InlineModelAdmin` in your new admin class.

Here an simple example : ::

  # The model
  from django.db import models
  from django.utils.translation import ugettext_lazy as _

  from zinnia.models import Entry

  class EntryImage(models.Model):
      """Image Model"""
      entry = models.ForeignKey(Entry, verbose_name=_('entry'))

      image = models.ImageField(_('image'), upload_to='uploads/gallery')
      title = models.CharField(_('title'), max_length=250)
      description = models.TextField(_('description'), blank=True)

      def __unicode__(self):
          return self.title

  # The admin

  from django.contrib import admin

  from zinnia.models import Entry
  from zinnia.admin import EntryAdmin
  from gallery.models import EntryImage

  class EntryImageInline(admin.TabularInline):
      model = EntryImage

  class EntryAdminImage(EntryAdmin):
      inlines = (EntryImageInline,)

  admin.site.unregister(Entry)
  admin.site.register(Entry, EntryAdminImage)

Another and better solution is to extend the :class:`~zinnia.models.Entry`
model like described in :doc:`extending_entry_model`.


.. _`specifying a template directory`: https://docs.djangoproject.com/en/dev/ref/templates/api/#loading-templates
.. _`customizing the look and feel`: https://docs.djangoproject.com/en/dev/intro/tutorial02/#customize-the-admin-look-and-feel
.. _`Zinnia-theme-html5`: https://github.com/Fantomas42/zinnia-theme-html5
.. _`Django Blog Quintet`: https://github.com/franckbret/django-blog-quintet
.. _`django-threadcomments`: https://github.com/HonzaKral/django-threadedcomments
.. _`MarkDown`: http://daringfireball.net/projects/markdown/
.. _`Textile`: http://redcloth.org/hobix.com/textile/
.. _`reStructuredText`: http://docutils.sourceforge.net/rst.html
.. _`sorl.thumbnail`: http://thumbnail.sorl.net/
