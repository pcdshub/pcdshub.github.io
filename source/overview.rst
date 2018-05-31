Overview
########
The pcdshub python 3 software stack is being developed with the following
design goals:

- Modules should have a well-defined, restrained scope.
- Code should be readable, reusable, and sharable.
- Each piece of data should have a canonical source for all applications.
- Deployment and testing should be automated.
- The open source community should be maximally leveraged.

These design goals result in a matrix of github repositories and python
modules that are individually understandable, but together obscure the
bigger picture. The goal of this page is to explain at a high level how
each application works and what each repository does.

First, Some Definitions
=======================
An :ref:`application <Applications>` is any python code that runs
stand-alone to accomplish a task. These may rely on
:ref:`modules <Modules>`, which are any python files that provide an
importable API and a ``setup.py`` file to install themselves into a
python environment. Many repositories are both applications and
modules, providing both a stand-alone script and an API
for other applications to leverage. This document will also cover some
:ref:`external modules <External Modules>` that are very relevant to
the needs of LCLS.

Applications
============
This section will list our applications and explain the motivations
behind them, as well as their dependencies.
This includes only repositories that are hosted on pcdshub with a
corresponding online documentation page.

hutch-python
------------

pcds-envs
---------

pcds-recipes
------------


Modules
=======
This section will list our modules and explain the motivations
behind them, as well as their dependencies.
This includes only repositories that are hosted on pcdshub with a
corresponding online documentation page.

typhon
------

pcdsdevices
-----------

transfocate
-----------

pcdsdaq
-------

happi
-----

device_config
-------------

External Modules
================
This section will list some of the more site-specific external modules we use
and explain the motivations behind the modules and behind why we use them.

bluesky
-------

opyhd
-----

pydm
----
