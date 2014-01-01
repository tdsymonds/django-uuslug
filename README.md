Django Uuslug
====================

**A Django slugify application that guarantees `Uniqueness` and handles `Unicode`**

[![build-status-image-travis]][travis]
[![build-status-image-fury]][fury]
[![build-status-image-pypi]][pypi]


Overview
========

A Django slugify application that guarantees uniqueness and handles unicode.
UUSlug == (``U``nique + ``U``nicode) Slug

How to install
==================

    1. easy_install django-uuslug
    2. pip install django-uuslug
    3. git clone http://github.com/un33k/django-uuslug
        a. cd django-uuslug
        b. run python setup.py
    4. wget https://github.com/un33k/django-uuslug/zipball/master
        a. unzip the downloaded file
        b. cd into django-uuslug-* directory
        c. run python setup.py

How to use
=================

Unicode Test

    from uuslug import slugify

    txt = "This is a test ---"
    r = slugify(txt)
    self.assertEquals(r, "this-is-a-test")

    txt = "This -- is a ## test ---"
    r = slugify(txt)
    self.assertEquals(r, "this-is-a-test")

    txt = 'C\'est déjà l\'été.'
    r = slugify(txt)
    self.assertEquals(r, "cest-deja-lete")

    txt = 'Nín hǎo. Wǒ shì zhōng guó rén'
    r = slugify(txt)
    self.assertEquals(r, "nin-hao-wo-shi-zhong-guo-ren")

    txt = 'Компьютер'
    r = slugify(txt)
    self.assertEquals(r, "kompiuter")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt)
    self.assertEquals(r, "jaja-lol-mememeoo-a")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=9)
    self.assertEquals(r, "jaja-lol")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=15)
    self.assertEquals(r, "jaja-lol-mememe")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=50)
    self.assertEquals(r, "jaja-lol-mememeoo-a")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=15, word_boundary=True)
    self.assertEquals(r, "jaja-lol-a")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=19, word_boundary=True)
    self.assertEquals(r, "jaja-lol-mememeoo")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True)
    self.assertEquals(r, "jaja-lol-mememeoo-a")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True, separator=".")
    self.assertEquals(r, "jaja.lol.mememeoo.a")

    txt = 'jaja---lol-méméméoo--a'
    r = slugify(txt, max_length=20, word_boundary=True, separator="ZZZZZZ")
    self.assertEquals(r, "jajaZZZZZZlolZZZZZZmememeooZZZZZZa")

Uniqueness Test

    Override your object's save method with something like this (models.py)

    from django.db import models
    from uuslug import uuslug

    class CoolSlug(models.Model):
        name = models.CharField(max_length=100)
        slug = models.CharField(max_length=200)

        def __unicode__(self):
            return self.name

        def save(self, *args, **kwargs):
            # self.slug = uuslug(self.name, instance=self, separator="_") # optional non-dash separator
            self.slug = uuslug(self.name, instance=self)
            super(CoolSlug, self).save(*args, **kwargs)


    Note: You can also specify the start number.
    Example:
        self.slug = uuslug(self.name, instance=self, start_no=2)
        # the second slug should start with "-2" instead of "-1"

    name = "john"
    c = CoolSlug.objects.create(name=name)
    c.save()
    print c.slug # => "john"

    c1 = CoolSlug.objects.create(name=name)
    c1.save()
    print c1.slug # => "john-1"

    c2 = CoolSlug.objects.create(name=name)
    c2.save()
    print c2.slug # => "john-2"


    # If you need truncation of your slug, here is an example
    class SmartTruncatedSlug(models.Model):
        name = models.CharField(max_length=19)
        slug = models.CharField(max_length=19)

        def __unicode__(self):
            return self.name

        def save(self, *args, **kwargs):
            self.slug = uuslug(self.name, instance=self, start_no=9, max_length=19, word_boundary=True)
            super(SmartTruncatedSlug, self).save(*args, **kwargs)

        # Let's test it
        name = 'jaja---lol-méméméoo--a'

        obj = SmartTruncatedSlug.objects.create(name=name)
        print obj.slug # "jaja-lol-mememeoo"  --- where 19 is max_length (first slug, no duplicate yet)

        obj = SmartTruncatedSlug.objects.create(name=name)
        print obj.slug # "jaja-lol-mememeoo-9" --- where 19 is max_length, start_no = 9

        obj = SmartTruncatedSlug.objects.create(name=name)
        print obj.slug # "jaja-lol-mememeo-10" -- where 19 is max_length, smart appending "-10"


Running the tests
=================

To run the tests against the current environment:

    python manage.py test


Running the tests
====================

To run the tests against the current environment:

    python manage.py test


License
====================

Protected by ([BSD](LICENSE.md))


[build-status-image-travis]: https://secure.travis-ci.org/un33k/django-uuslug.png?branch=master
[travis]: http://travis-ci.org/tomchristie/django-uuslug?branch=master

[build-status-image-fury]: https://badge.fury.io/py/django-uuslug.png
[fury]: http://badge.fury.io/py/django-uuslug

[build-status-image-pypi]: https://pypip.in/d/django-uuslug/badge.png
[pypi]: https://crate.io/packages/django-uuslug?version=latest

