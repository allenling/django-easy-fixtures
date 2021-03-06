django-easy-fixture
===================
.. figure:: https://travis-ci.org/allenling/django-easy-fixture.svg?branch=master

python2.7, python3.5

1.8<= Django <= 1.10

**install: pip install django-easy-fixture**

That is a easy, simple tool to help you to fill your fixture dict with some spam datas

Make your fixture dict to be a completely available django fixture that **you do not have to worry about any unqiue, unqie_together, just pk**

**The pk must be defined by you!**

**Just support all the fields defined by Django, and do not support any customization field.** 

**Maybe** support the customization field in the future.


1. get a fixture dict
---------------------

.. code-block:: python

   # in template.py
   fixtures_template={'auth.User': [{'pk': 1}]}

   # in other.py
   from easy_fixture.easy_fixture import EasyFixture
   from template import fixtures_template

   ef = EasyFixture(fixtures_template)
   fixtures_dict = ef.output()

2. use as a django app command
------------------------------

.. code-block:: python

   # in template.py
   fixtures_template = {'auth.User': [{'pk': 1}]}

   # in your settings.py
   INSTALLED_APPS = ('other apps',
                     'easy_fixture',
                     )
run make_fixture command
 
.. code-block:: python

   python manage.py make_fixture template.fixtures_template > /path/to/fixture.json

Must pass the module path and fixture variable, like module.to.fixture.Variable_name

**do not support old style**

.. code-block:: python

   python manage.py make_fixture template > /path/to/fixture.json

3. use in test
--------------

In your testCase, call EasyFixture.load_into_testcase in your setUpTestData, setUpClass, setUp or anywhere you want to load your fixture data. 

.. code-block:: python

   FIXTURE_DICT = {'auth.User': [{'pk': 1}]}

   class MyTestCase(TestCase):
       
       @classmethod
       def setUpTestData(cls):
           ef = EasyFixture(FIXTURE_DICT)
           ef.load_into_testcase()
           TestCase.setUpTestData()
       
       def tes_what_you_want(self):
           pass


**deprecated**

.. code-block:: python

   from easy_fixture.easy_fixture import FixtureFileGen

   class MyCase(TestCase):
      fixtures = FixtureFileGen(['my.fixture.template.module'])

4. how to loaddata to db
------------------------

Creating a temp file, and call loaddata command for now.

And, maybe we could simply

1. disable database constraint check

2. call serializers.deserialize to deserialize fixture datas, and save all objects

3. check constraint after we have save all objects 

4. finally, reset database sequence

.. code-block:: python

   # disable database constraint check
   with connection.constraint_checks_disabled():
       # save objects
       objects = serializers.deserialize(ser_fmt, fixture_data, using=self.using, ignorenonexistent=self.ignore)
       for obj in objects:
           obj.save()
   
   # check constraints
   connection.check_constraints(table_names=table_names)
   
   # reset sequence
   sequence_sql = connection.ops.sequence_reset_sql(no_style(), self.models)

