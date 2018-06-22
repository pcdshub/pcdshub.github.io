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
============= ================================================================
Repository    `amo <https://github.com/pcdshub/amo>`_,
              `sxr <https://github.com/pcdshub/sxr>`_,
              `xpp <https://github.com/pcdshub/xpp>`_,
              `xcs <https://github.com/pcdshub/xcs>`_,
              `mfx <https://github.com/pcdshub/mfx>`_,
              `cxi <https://github.com/pcdshub/cxi>`_,
              and `mec <https://github.com/pcdshub/mec>`_

How we use it We put softlinks like ``mfx3`` in the hutch opr's path. These
              link to scripts like ``mfxpython`` in the repository that launch
              a convenient ``IPython`` environment for controlling the hutch
              and automating DAQ data collection. The repository is used to
              host hutch-specific classes, devices, and configurations that
              may later be merged into a shared module.

Motivation    We need dedicated landing areas for hutch-specific and
              experiment-specific code to satisfy the needs of a constantly
              shifting environment. This can't go into a module because the
              module can't control which python environment you launch it
              from.

How it works  A version file e.g. ``mfxversion`` chooses a shared conda
              environment and may set up development package overrides. The
              ``mfxpython`` script enters this environment using
              :ref:`hutch-python` and a brief ``conf.yml`` configuration file.
              These coordinate the loading of shared services and provide
              hooks for the hutch-specific code.

Dependencies  This may vary between hutches, but all hutches rely on
              :ref:`hutch-python` as the launcher and configuration
              reader. All of these repositories were created from the
              ``hutch-python --create reponame`` template function.
============= ================================================================

hutch-python
------------
============= ================================================================
Repository    `hutch-python <https://github.com/pcdshub/hutch-python>`_,

How we use it Scripts like ``mfxpython`` call on the ``hutch-python``
              application with a hutch-specific ``conf.yaml`` file, as well as
              hutch-specific ``mfx/beamline.py`` and ``experiments`` folder.

Motivation    The load process for each hutch is essentially identical. This
              should be handled in shared code with simple hooks for
              hutch-specific and experiment-specific additions. The best
              alternaive is using ``IPython`` profiles which can get messy and
              hard to manage, as we would need to occassionally go around and
              rewrite boilerplate code.

How it works  A shared startup script is seeded with the contents of the
              ``conf.yml`` file. Shared operations are done the same way and
              then the ``beamline.py`` and ``experiments`` user code is
              invoked.

Dependencies  This is used by the :ref:`Hutch-specific repositories` to 

              Relies on most of our modules:

              - Uses :ref:`happi` to load from the beamline device database
                and from the questionnaire experiment devices
              - Uses :ref:`pcdsdevices` for the device definitions and to set
                up position presets
              - Uses :ref:`pcdsdaq` to control the data acquisition system
              - Uses :ref:`lightpath` as a command-line interface for checking
                if anything is blocking the beam path
              - Uses :ref:`elog` to post to the experiment logbooks
============= ================================================================

lightpath
---------
============= ================================================================
Repository    `lightpath <https://github.com/pcdshub/lightpath>`_,

How we use it An environment with ``lightpath`` installed has a ``lightpath``
              script that will open the GUI. This can be use to visually
              inspect the light path. This doubles as an application and a
              module, providing an importable interface that is used in other
              places, such as :ref:`hutch-python`.

Motivation    Help the user find which objects are in the beam, and which are
              not. This can be used to clear the beamline and to check if you
              expect beam to be incident on your imager. Unlike older
              software with the same purpose, this is extremely configurable
              and simple to keep up-to-date using :ref:`happi`.

How it works  This uses the z-position and beamline metadate from :ref:`happi`
              to sort devices by position along the beamline. It relies on the
              in/out interface from :ref:`pcdsdevices` to determine whether
              each device is in the beam.

Dependencies  - Uses :ref:`pydm` for the GUI
              - Uses :ref:`happi` for device loading
              - Uses modules like :ref:`pcdsdevices` to define device classes
============= ================================================================

pmgr
----
============= ================================================================
Repository    `pmgr <https://github.com/pcdshub/pmgr>`_,

How we use it This is the Parameter Manager, ``pmgr`` for short. This isn't
              installed in an environment (yet), but is used as a stand-alone
              GUI for keeping track of device parameters. It is used in the
              old python cli for the same purpose, but this has yet to be
              ported.

Motivation    We need a place to keep track of and restore parameters to ease
              the deployment and redeployment of motors for changing
              experimental needs.

How it works  ``pmgr`` provides a qt gui with a mysql server backend that
              stores all of the parameters.

Dependencies  ``pmgr`` does not depend on our other modules, but it does
              depend on our patched version of ``pyqt`` to make the tables
              work.
============= ================================================================

pcds-envs
---------
============= ================================================================
Repository    `pcds-envs <https://github.com/pcdshub/pcds-envs>`_,

How we use it This is used to track our shared environment changes, and to
              anticipate and catch integration problems with bringing all of
              our packages together.

Motivation    We need to do this in a structured, semi-automated way to
              minimize upkeep and catch mistakes.

How it works  ``packages.txt`` is updated to add new packages.
              ``stage_release.sh`` is ran to build a new environment from the
              packages list and push it to github. We make a PR and discuss.
              Along with the PR, an automated test is ran to check that all
              of our pcdshub modules pass their individual automated tests
              with the full environment (this also runs daily).
              If we like the new environment, we merge the PR and make a tag.
              We use ``apply_release.sh`` to put the new tag into the shared
              environment.

Dependencies  This only depends on ``Python`` and ``Conda``
============= ================================================================

pcds-recipes
------------
============= ================================================================
Repository    `pcds-recipes <https://github.com/pcdshub/pcds-recipes>`_,

How we use it This is used to build non-pcdshub conda packages and make sure
              they get into the pcds-tag channel.

Motivation    It is unsustanable and unstable to rely on special channels for
              our conda environments. By limiting to the ``defaults``,
              ``conda-forge``, and our own ``pcds-tag`` it becomes easy to
              specify our environment setups.

How it works  Recipes are placed into the repository and ``build.py`` builds
              and uploads them.

Dependencies  This only depends on ``Python`` and ``Conda``
============= ================================================================


Modules
=======
This section will list our modules and explain the motivations
behind them, as well as their dependencies.
This includes only repositories that are hosted on pcdshub with a
corresponding online documentation page.

typhon
------
============= ================================================================
Repository    `typhon <https://github.com/pcdshub/typhon>`_,

How we use it Automatically generate screens from ophyd devices.

Motivation    We need new :ref:`pydm` screens and these are great starting
              points. The :ref:`ophyd` device structure is very useful for
              organizing device properties into logical groups.

How it works  Group device components by kind (e.g. hinted, normal,
              configuration) and sort into tabs, etc. accordingly. Provide
              tools on every generated screen.

Dependencies  :ref:`pydm` and :ref:`ophyd`
============= ================================================================

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
