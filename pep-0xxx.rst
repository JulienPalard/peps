PEP: XXX
Title: Python Documentation Translations
Version: $Revision$
Last-Modified: $Date$
Author: Victor Stinner <vstinner@redhat.com>,
        Inada Naoki <songofacandy@gmail.com>,
        Julien Palard <julien@palard.fr>
Status: Draft
Type: Process
Created: 04-Mar-2017
Post-History:


Abstract
========

The intent of this PEP is to make existing translations of the Python
Documentation more accessible and discoverable.  By doing so,
motivating new translators and new translations.


Motivation
==========

On the french ``#python-fr`` IRC channel on freenode, it's common to
meet people who don't speak english and so are unable to read the
Python official documentation.  Python wants to be widely available,
for all users, in any language: that's also why Python 3 now allows
any non-ASCII identifiers:
https://www.python.org/dev/peps/pep-3131/#rationale

There are a least 3 groups of people who are translating the Python
documentation in their mother language (french [16]_ [17]_ [18]_,
japanese [19]_ [20]_, spanish [21]_), even though their translation
are not visible on d.p.o.  This PEP proposes to move translations on
docs.python.org so they can easily be found by newcomers and potential
translators.

Japanese team currently (March 2017) translated ~80% of the
documentation, french team ~20%. French translation went from 6% to
23% in 2016 [13]_ with 7 contributors [14]_, proving a translation
team can be faster than documentation mutates.


Quoting Xiang Zhang:

> I have seen several groups trying to translate part of our official
  doc. But their efforts are disperse and quickly become lost because
  they are not organized to work towards a single common result and
  their results are hold anywhere on the Web and hard to find. An
  official one could help ease the pain.


Rationale
=========

Translation
-----------

Bug tracker
'''''''''''

As all translation have their own github project, they should use the
associated github issue tracker instead of b.p.o, avoiding a mix of
languages on b.p.o.  Language team coordinators are responsible of
redirecting from b.p.o to their specific github issue tracker every
issue opened for their language.


Branches
''''''''

Translation teams should focus on last stable versions, and use tools
(scripts, translation memory, …) to automatically translate what is
done in one branch to other branches.

As documentation Makefile won't be modified for old realses like 3.3,
and passing options to sphinx is mandatory to change the language,
only 2.7, 3.5, and 3.6 branches will be translated [12]_.

Default (master) branch may not be translated actively, but as
translation memory may translate a consequent part of it, if a team
has nothing else to translate, and want to do it, they can. In any
way, docsbuild-scripts have to build it, so a team can preview their
translations before a Python release, to be ready.


Hosting
-------

Domain Name and URL
'''''''''''''''''''

We can distinguish the lang by changing one of: CCTLD, path segment,
or subdomain.

Buying a CCTLD for each translations is expensive, time-consuming, and
sometimes already used.

Using subdomains like "es.docs.python.org" or "docs.es.python.org"
does not looks natural or clear.

So we should put use the path to contain the language information,
like "docs.python.org/de/".

As for version, sphinx-doc does not support compiling for multiple
languages, so we'll have full builds rooted under a path, exactly like
we're already doing with versions.  So we can have
"docs.python.org/de/3.6/" or "docs.python.org/3.6/de/".  As existing
version are changing more often than existing languages, we should
prefer "docs.python.org/de/3.6/".

So we should use the following pattern:
"docs.python.org/LANGUAGE_TAG/VERSION/".

See `Language Tag`_ for the rationale about it.

We should not move current documentation to "/en/" but we can redirect
"/en/" to "/" to be kind with humans manually replacing a language tag
with "en".

Language Tag
''''''''''''

Typical notation for language tags is the IETF Language Tag [3]_,
[4]_, based on ISO 639, alghough gettext uses ISO 639 tags with
underscores instead of dashes to join tags [5]_.

It is more common to see dashes instead of underscores in URLS [6]_,
so we should use IETF language tags, even if sphinx uses gettext
internally: URL are not meant to leak underlying implementation.

It's uncommon to see capitalized letters in URLs, so it may hurt
readability by attracting the eye on it:
"https://docs.python.org/pt-BR/3.6/library/stdtypes.html".  RFC 5646
(Tags for Identifying Languages (IETF)) section-2.1 [7]_ tells the
tags are not case sensitive.  As RFC allows us to do so and it enhances
readability, we should use lowercased tags.

It's redundant to display both language and country code if they're
the same, typically "de-DE", "fr-FR", although it make sense,
respectively "Deutch as spoken in Germany" and "French as spoken in
France", it's not a usefull information for the reader.  So we may drop
those redundencies.  We should obviously keep the country part when it
make sense like "pt-BR" for "Portuguese as spoken in Brazil".

So we should use IETF language tags, lowercased, like ``/fr/``,
``/pt-br/``, ``/de/`` and so on.


Fetching And Building Translations
''''''''''''''''''''''''''''''''''

Currently a script is building the documentation [8]_.  This script
should be modified to fetch and build translations.

Building new translations is like building new versions, so we're
adding complexity, but not that much.

Two steps should be configurable distinctively: Build a new language,
and add it to the language picker.  This allows a transition phase
between "we accepted the language" and "it is translated enough to be
made public".

From the translations repositories, only the ``.po`` files should be
opened by the docsbuild-script to keep the attack surface and probable
bugs sources at a minimum.  This mean no translation can patch sphinx
to advertise their translation tool.  (This specific feature should be
handled by sphinx anyway [9]_).


Community
---------

Mailing List
''''''''''''

We may create a new mailing list or use an existing one, like
`i18n-sig`_ or `doc-sig`_.

i18n-sig looks oriented towards i18n APIs [1]_, but doc-sig looks the
right place to speak about this [2]_, so we don't have to create a new
mailing list for this, we should use doc-sig.

.. _i18n-sig: https://mail.python.org/mailman/listinfo/i18n-sig
.. _doc-sig: https://mail.python.org/mailman/listinfo/doc-sig


Chat
''''

Python community being highly active on IRC, we should create a new
IRC channel on freenode, typically #python-doc for consistency with
the mailing list name.

Each language coordinator can organize its own team, even by choosing
another chat system if the local usage asks for it.  As local teams
will write in their native languages, we don't want each team in a
single channel, and it's also natural for the local teams to reuse
their local channels like "#python-fr" for french translators.


Repository for Po Files
'''''''''''''''''''''''

Each language should have its own repository on the `Python github
organization`_.

Each repository should be named according to the following template:
``python-doc-LANGUAGE_TAG``.  See `Language Tag`_ below.

.. _Python github organization: https://github.com/python/

The docsbuild-scripts may enforce this rule by refusing to fetch
outside of the Python organization.

The CLA bot may be used on the translation repositories, but with a
limited effect as local coordinators may synchronize themselves
translations from an external tool like transifex, loosing in the
process who translated what.

Version can be hosted on different repositories, different directories
or different branches.  Storing them on different repositories will
probably pollute the Python github organization.  As it is typical and
natural to use branches to separate versions, branches should be used
to do so.


Translation tools
'''''''''''''''''

Most of the translation work is actually done on Transifex [15]_.

TODO::

    But Mozilla and Fedora have their own translation web site.  I
    (Inada) want to evaluate them, because they are Python friends.
    Requirement (Julien): The tool may help us validating each
    translators have signed the CLA.

    - https://pontoon.mozilla.org/
    - http://zanata.org/


Language Team
'''''''''''''

Each language team should have one coordinator responsible to:

- Manage the team
- Choose and manage the tools its team will use (chat, mailing list, …)
- Ensure contributors agree with the CLA
- Ensure quality (grammar, vocabulary, consistency, filtering spam, ads, …)
- Do redirect to github issue tracker issues related to its
  language on bugs.python.org


License should be PSF License, and copyright should be able to
transferred to PSF later.


Alternatives
------------

Simplified English
''''''''''''''''''

It would be possible to introduce a "simplified english" version like
wikipedia did [10]_, as discussed on python-dev [11]_, targetting
english learners and childrens.

Pros: It yields a single other translation, theorically readable by
everyone, and reviewable by current maintainers.

Cons: Subtle details may be lost, and translators from english to english
may be hard to find as stated by Wikipedia:

> The main English Wikipedia has 5 million articles, written by nearly
140K active users; the Swedish Wikipedia is almost as big, 3M articles
from only 3K active users; but the Simple English Wikipedia has just
123K articles and 871 active users.  That's fewer articles than
Esperanto!


Overall Procedure
=================

Migrate Github Repositories
---------------------------

We (authors of this PEP) already own french and japanese git
repositories, so moving them to the Python organization will not be a
problem. We'll however follow the `New Translation Procedure`_.


Patch docsbuild-scripts to Compile Translations
-----------------------------------------------

Docsbuild-script should be patched, in a way similar than `Pull
Request #8`_, but simplier as all repositories will be hosted in the
same organization, and named following an identical template, and
versions stored in branches.

.. _Pull Request #8: https://github.com/python/docsbuild-scripts/pull/8

Patched docsbuild-scripts may start building translations when ready,
as translations will only be made public by the implementation of the
language picker.


Create sphinx-doc Language Picker
---------------------------------

Highly similar to the version picker we have to implement a language
picker. This language picker should be configurable to hide or show a
given language.


Enhance rendering of untranslated fuzzy translations
----------------------------------------------------

It's an opened sphinx issue [9]_, but we'll need it so we'll have to
work on it.  Translated, fuzzy, and untranslated paragraphs should be
differentiated.  (Fuzzy paragraphs have to warn the reader what it's
reading may be out of date.)


New Translation Procedure
=========================

Designate a Coordinator
-----------------------

The first step is to designate a coordinator, see `Language Team`_.

The coordinator must sign the CLA.

TODO: Coordinators should probably be listed, but where?


Create github repository
------------------------

A repository on the github Python organization should be created,
named: python-doc-LANGUAGE_TAG, see `Repository For Po
Files`_.  Coordinator should be granted push permission on this
repository.


Add translation in docsbuild-scripts
------------------------------------

As soon as the translation hits its firsts commits, docsbuild-scripts
can be told to build it, so translators (using external tools like
Transifex) can review their work without building the documentation
locally, see `Fetching And Building Translations`_.


Add translation to the language picker
--------------------------------------

As soon as the translation hits:

- 100% of bugs.html with proper links to the language repository
  issue tracker.
- 100% of tutorial
- 100% of library/functions (builtins)

the translation can be added to the language picker.


Previous discussions
====================

- `[Python-ideas] Cross link documentation translations (January, 2016)`_
- `[Python-ideas] Cross link documentation translations (January, 2016)`_
- `[Python-ideas] https://docs.python.org/fr/ ? (March 2016)`_


.. _[Python-ideas] Cross link documentation translations (January, 2016):
   https://mail.python.org/pipermail/python-ideas/2016-January/038010.html

.. _[Python-Dev] Translated Python documentation (Febrary 2016):
   https://mail.python.org/pipermail/python-dev/2017-February/147416.html

.. _[Python-ideas] https://docs.python.org/fr/ ? (March 2016):
   https://mail.python.org/pipermail/python-ideas/2016-March/038879.html


References
==========

.. [1] [I18n-sig] Hello Python members, Do you have any idea about
   Python documents?
   (https://mail.python.org/pipermail/i18n-sig/2013-September/002130.html)

.. [2] [Doc-SIG] Localization of Python docs
   (https://mail.python.org/pipermail/doc-sig/2013-September/003948.html)

.. [3] Tags for Identifying Languages
   (http://tools.ietf.org/html/rfc5646)

.. [4] IETF language tag
   (https://en.wikipedia.org/wiki/IETF_language_tag)

.. [5] GNU Gettext manual, section 2.3.1: Locale Names
   (https://www.gnu.org/software/gettext/manual/html_node/Locale-Names.html)

.. [6] Semantic URL: Slug
   (https://en.wikipedia.org/wiki/Semantic_URL#Slug)

.. [7] Tags for Identifying Languages: Formatting of Language Tags
   (https://tools.ietf.org/html/rfc5646#section-2.1.1)

.. [8] Docsbuild-scripts github repository
   (https://github.com/python/docsbuild-scripts/)

.. [9] i18n: Highlight untranslated paragraphs
   (https://github.com/sphinx-doc/sphinx/issues/1246)

.. [10] Wikipedia: Simple English
   (https://simple.wikipedia.org/wiki/Main_Page)

.. [11] Python-dev discussion about simplified english
   (https://mail.python.org/pipermail/python-dev/2017-February/147446.html)

.. [12] Passing options to sphinx from Doc/Makefile
   (https://github.com/python/cpython/commit/57acb82d275ace9d9d854b156611e641f68e9e7c)

.. [13] French translation progression
   (https://mdk.fr/pycon2016/#/11)

.. [14] French translation contributors
   (https://github.com/AFPy/python_doc_fr/graphs/contributors?from=2016-01-01&to=2016-12-31&type=c)

.. [15] Python-doc on Transifex
   (https://www.transifex.com/python-doc/)

.. [16] French translation
   (https://www.afpy.org/doc/python/)

.. [17] French translation github
   (https://github.com/AFPy/python_doc_fr)

.. [18] French mailing list
   (http://lists.afpy.org/mailman/listinfo/traductions)

.. [19] Japanese translation
   (http://docs.python.jp/3/)

.. [20] Japanese github
   (https://github.com/python-doc-ja/python-doc-ja)

.. [21] Spanish translation
   (http://docs.python.org.ar/tutorial/3/index.html)

Copyright
=========

This document has been placed in the public domain.




..
   Local Variables:
   mode: indented-text
   indent-tabs-mode: nil
   sentence-end-double-space: t
   fill-column: 70
   coding: utf-8
   End:
