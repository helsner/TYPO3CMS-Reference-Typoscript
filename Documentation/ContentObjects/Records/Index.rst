.. include:: /Includes.rst.txt
.. index::
   Content objects; RECORDS
   RECORDS
.. _cobj-records:

=======
RECORDS
=======

.. contents::
   :local:
   :depth: 1


.. index:: RECORDS; Properties
.. _cobj-records-properties:

Properties
==========

.. container:: ts-properties

  ============================ ================================================================ ======= ==================
  Property                     Data types                                                       stdWrap Default
  ============================ ================================================================ ======= ==================
  categories_ =                *categories-list* /:ref:`stdWrap <stdwrap>`                      yes
  conf_ =                      :ref:`cObject <data-type-cobject>`                               no
  dontCheckPid_ =              boolean /:ref:`stdWrap <stdwrap>`                                yes     0
  source_ =                    *records-list* /:ref:`stdWrap <stdwrap>`                         yes
  stdWrap_ =                   :ref:`stdwrap`                                                   yes
  tables_ =                    *list of tables* /:ref:`stdWrap <stdwrap>`                       yes
  wrap_ =                      :ref:`wrap <data-type-wrap>` /:ref:`stdWrap <stdwrap>`           yes
  ============================ ================================================================ ======= ==================


.. _cobj-records-introduction:

Introduction
============

This object is meant for displaying lists of records from a variety of
tables. Contrary to the :ref:`CONTENT <cobj-content>` object, it does
not allow very fine selections of records (as it has no "select"
property).

The register-key :code:`SYS_LASTCHANGED` is updated with the tstamp-field of
the records selected which has a higher value than the current.

.. note::

   Records with parent ids (pid's) for non-accessible pages
   (that is hidden, timed or access-protected pages) are normally not
   selected. Pages may be of any type, except recycler. Disable the check
   with the :ref:`dontCheckPid option <cobj-records-properties-dontcheckpid>`.


.. _cobj-records-details:

Property details
================

.. contents::
   :local:
   :depth: 1


.. index:: RECORDS; source
.. _cobj-records-properties-source:

source
------

.. container:: table-row

   Property
         source

   Data type
         *records-list* /:ref:`stdWrap <stdwrap>`

   Description
         List of record-id's, optionally with prepended table-names.

         **Example:**

         .. code-block:: typoscript

            source = tt_content_34, 45, tt_links_56


.. index:: RECORDS; categories
.. _cobj-records-properties-categories:

categories
----------

.. container:: table-row

   Property
         categories

   Data type
         *categories-list* /:ref:`stdWrap <stdwrap>`

   Description
         Comma-separated list of system categories uid's.
         Records related to these categories will be retrieved
         and made available for rendering.

         Only records from the tables defined in the
         :ref:`tables property <cobj-records-properties-tables>` will be retrieved.

         This property has the following sub-property:

         relation
           Name of the categories-relation field to use for
           building the list of categorized records, as there can
           be several such fields on a given table.

         .. warning::

            If both :code:`source` and :code:`categories` properties are defined,
            the :code:`source` property will take precedence, as it is considered
            more precisely targeted.


.. index:: RECORDS; tables
.. _cobj-records-properties-tables:

tables
------

.. container:: table-row

   Property
         tables

   Data type
         *list of tables* /:ref:`stdWrap <stdwrap>`

   Description
         List of accepted tables. For items listed in the
         :ref:`source <cobj-records-properties-source>` property
         which are not prepended with a table name, the first table
         will be used.

         Records from tables configured in :ref:`conf <cobj-records-properties-conf>`
         are also allowed.

         **Example:**

         .. code-block:: typoscript

            tables = tt_content, tt_address, tt_links
            conf.tx_myexttable = TEXT
            conf.tx_myexttable.value = Hello world

         This adds the tables tt\_content, tt\_address, tt\_links and
         tx\_myexttable.


.. index:: RECORDS; conf
.. _cobj-records-properties-conf:

conf
----

.. container:: table-row

   Property
         conf.[*table name*]

   Data type
         :ref:`cObject <data-type-cobject>`

   Description
         Configuration array, which defines the rendering for records from
         table *table name*.

   Default
         If this is *not* defined, the rendering of the records is done with
         the top-level object *[table name]* - just like when ".renderObj" is
         not set for the cObject :ref:`CONTENT <cobj-content>`!


.. index:: RECORDS; dontCheckPid
.. _cobj-records-properties-dontcheckpid:

dontCheckPid
------------

.. container:: table-row

   Property
         dontCheckPid

   Data type
         boolean /:ref:`stdWrap <stdwrap>`

   Description
         Normally a record cannot be selected, if its parent page (pid) is not
         accessible for the website user. This option disables that check.

   Default
         0


.. _cobj-records-properties-wrap:

wrap
----

.. container:: table-row

   Property
         wrap

   Data type
         :ref:`wrap <data-type-wrap>` /:ref:`stdWrap <stdwrap>`

   Description
         Wraps the output. Executed before :ref:`stdWrap <cobj-records-properties-stdwrap>`.


.. _cobj-records-properties-stdwrap:

stdWrap
-------

.. container:: table-row

   Property
         stdWrap

   Data type
         :ref:`->stdWrap <stdwrap>`

   Description
         Executed after :ref:`wrap <cobj-records-properties-wrap>`.


.. _cobj-records-properties-cache:

cache
-----

.. include:: ../../DataTypes/Properties/Cache.rst.txt


.. _cobj-records-examples:

Examples
========


.. index:: RECORDS; Selection with source
.. _cobj-records-examples-source:

Selection with source
---------------------

The following example would display some related content
referenced from the :guilabel:`page properties`.

.. code-block:: typoscript

   page.42 = RECORDS
   page.42 {
      source.field = tx_examples_related_content
      tables = tt_content
   }

Since no :code:`conf` property is defined, the rendering will
look for a top-level TypoScript object bearing the name of the
table to be rendered (e.g. "tt_content").


.. index:: RECORDS; Selection with source
.. _cobj-records-examples-source-ii:

Selection with source II
------------------------

.. code-block:: typoscript

	20 = RECORDS
	20 {
		source = 10,12
		dontCheckPid = 1
		tables = tt_content
	}

This example loads the content elements with the UIDs 10 and 12 no
matter where these elements are located and whether these pages are
accessible for the current user.


.. index:: RECORDS; Selection with categories
.. _cobj-records-examples-categories:

Selection with categories
-------------------------

If you want to display categorized content with a :typoscript:`RECORDS` object
you could do it like this::

	categorized_content = RECORDS
	categorized_content {
		categories.field = selected_categories
		categories.relation.field = category_field
		tables = tt_content
		conf.tt_content = TEXT
		conf.tt_content {
			stdWrap.field = header
			stdWrap.typolink.parameter = {field:pid}#{field:uid}
			stdWrap.typolink.parameter.insertData = 1
			stdWrap.wrap = <li>|</li>
		}
		wrap = <ul>|</ul>
	}

Contrary to the previous example, in this case the :code:`conf` property
is present and defines a very simple rendering of each content element
(i.e. the header with a direct link to the content element).

However, the same can be achieved with a :ref:`cobj-fluidtemplate` and
data processing. This way templating is much more flexible. See the following
example from the system extension :file:`fluid_styled_content`::

   tt_content.menu_categorized_content =< lib.contentElement
   tt_content.menu_categorized_content {
      templateName = MenuCategorizedContent
      dataProcessing {
         10 = TYPO3\CMS\Frontend\DataProcessing\DatabaseQueryProcessor
         10 {
            table = tt_content
            selectFields = tt_content.*
            groupBy = uid
            pidInList.data = leveluid : 0
            recursive = 99
            join.data = field:selected_categories
            join.wrap = sys_category_record_mm ON uid = sys_category_record_mm.uid_foreign AND sys_category_record_mm.uid_local IN(|)
            where.data = field:category_field
            where.wrap = tablenames='tt_content' and fieldname='|'
            orderBy = tt_content.sorting
            as = content
         }
      }
   }
