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

About this Page
===============
This page will explore the :ref:`applications <Applications>` and
:ref:`modules <Modules>` from `pcdshub <https://github.com/pcdshub>`_,
as well as some :ref:`external modules <External Modules>`, some of which
are developed at SLAC and some which are not.

For each project, this page will explain:

- The motivation behind the project: what problem is being solved?
- How the project works at a high level
- What other projects does this one rely on?
- What other projects rely on this one?

Definitions
===========

An application is any python code that runs stand-alone to accomplish
a task. These may rely on modules to accomplish their goals. A module
is code with an importable API and a means to install itself in a
python environment. Many repositories are both applications and
modules, providing both a stand-alone script and an API
for other applications to leverage.

Applications
============
This section will list our applications and explain the motivations
behind them, as well as their dependencies.
This includes only repositories that are hosted on pcdshub with a
corresponding online documentation page.

Hutch-specific Repositories
---------------------------
============ ===============================================================
Repository   `amo <https://github.com/pcdshub/amo>`_,
             `sxr <https://github.com/pcdshub/sxr>`_,
             `xpp <https://github.com/pcdshub/xpp>`_,
             `xcs <https://github.com/pcdshub/xcs>`_,
             `mfx <https://github.com/pcdshub/mfx>`_,
             `cxi <https://github.com/pcdshub/cxi>`_,
             and `mec <https://github.com/pcdshub/mec>`_

Motivation   These are the designated landing areas for hutch-specific and
             experiment-specific code. 

Usage        Call ``xpppython``, ``mfxpython``, etc. to start a specialized
             ``IPython`` session for a particular hutch to control beamlines
             and run experiments.

How it works   test

Dependencies This may vary between hutches, but all hutches rely on
             :ref:`hutch-python` as the launcher and configuration
             reader. All of these repositories were created from the
             ``hutch-python --create reponame`` template function.
============ ===============================================================

hutch-python
------------
`hutch-python <https://github.com/pcdshub/hutch-python>`_ is the launcher
for interactive ``IPython`` sessions that control the beamlines and experiment
setups.

Motivation
^^^^^^^^^^
The load process for each hutch is essentially identical. This should be
handled in shared code with simple hooks for hutch-specific and
experiment-specific additions.

How it Works
^^^^^^^^^^^^
pass


lightpath
---------

pmgr
----

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

hxrsnd
------

nabs
----

pcdsdaq
-------

happi
-----

device_config
-------------

elog
----

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
