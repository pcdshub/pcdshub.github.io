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

How it works  Groups device components by kind (e.g. hinted, normal,
              configuration) and sort into tabs, etc. accordingly. Provides
              tools on every generated screen.

Dependencies  :ref:`pydm` and :ref:`ophyd`
============= ================================================================

pcdsdevices
-----------
============= ================================================================
Repository    `pcdsdevices <https://github.com/pcdshub/pcdsdevices>`_,

How we use it To define :ref:`ophyd` device classes that correspond to lcls
              pcds EPICS IOCs. This is also where we put additional logic and
              cli niceties for interactive sessions with these devices.

Motivation    There must be a common place where these classes are defined
              so that all of our ``Python`` applications share PV structures
              and logic for each identical device class or instance.

How it works  Follow the :ref:`ophyd` rules to record which PVs are associated
              with each device class.

Dependencies  - :ref:`ophyd`
              - ``pyepics``, currently as the EPICS communication layer
============= ================================================================

transfocate
-----------
============= ================================================================
Repository    `transfocate <https://github.com/pcdshub/transfocate>`_,

How we use it To define an :ref:`ophyd` device class for the MFX transfocator.

Motivation    This is more complicated than a standard device and was being
              developed parallel to the :ref:`pcdsdevices` repository.

How it works  Defines the transfocator's PVs and some helper methods for
              putting lens combinations in and out.

Dependencies  :ref:`pcdsdevices`
============= ================================================================

hxrsnd
------
============= ================================================================
Repository    `hxrsnd <https://github.com/pcdshub/hxrsnd>`_,

How we use it To define :ref:`ophyd` device classes and :ref:`bluesky`
              scanning routines for the XCS split and delay.

Motivation    This is more complicated than a standard device and was being
              developed parallel to the :ref:`pcdsdevices` repository.

How it works  Defines the SND's PVs and some helper methods for running
              calibration scans and collecting data.

Dependencies  :ref:`pcdsdevices`, ``pswalker``
============= ================================================================

nabs
----
============= ================================================================
Repository    `nabs <https://github.com/pcdshub/nabs>`_,

How we use it To define lcls-specific :ref:`bluesky` utilities and to build
              out the :ref:`bluesky` automation framework.

Motivation    We want a cleaner version of the legacy ``pswalker`` module
              to have shared code between different applications that need
              to do similar things e.g. filter on beam drops, maximize
              signals, etc.

How it works  Defines an API for various plans, preprocessors, etc.

Dependencies  :ref:`bluesky`
============= ================================================================

pcdsdaq
-------
============= ================================================================
Repository    `pcdsdaq <https://github.com/pcdshub/pcdsdaq>`_,

How we use it To define a :ref:`bluesky`-compatible control layer for the
              LCLS1 data aquisition system (DAQ)

Motivation    We need to be able to control the DAQ. Doing it in a
              :ref:`bluesky`-compatible way means we can include the DAQ in
              any :ref:`bluesky` plan.

How it works  Wraps the ``pydaq.Control`` object and adheres closely to the
              :ref:`bluesky` interface. You can configure the daq, do runs,
              and include it as a readable device in scans.

Dependencies  :ref:`bluesky`, :ref:`ophyd`, ``pydaq``
============= ================================================================

happi
-----
============= ================================================================
Repository    `happi <https://github.com/pcdshub/happi`_,

How we use it To store all of our device metadata in :ref:`device_config` and
              reload these devices to create identical objects in every
              application.

Motivation    There must be a canonical way to load devices, consistent
              accross all of our python applications.

How it works  Defines container objects that represent real objects. These can
              be stored in json or mongodb and be used to generate real
              objects.

Dependencies  No pcdshub dependencies
============= ================================================================

device_config
-------------
============= ================================================================
Repository    `device_config <https://github.com/pcdshub/device_config`_,

How we use it As a central file store of all the :ref:`happi` device
              configuration information.

Motivation    We need to store the information somewhere until we have a
              mongodb set up.

How it works  Stores a single json file.

Dependencies  No pcdshub dependencies
============= ================================================================

elog
----
============= ================================================================
Repository    `elog <https://github.com/pcdshub/elog`_,

How we use it To post information to the experiment elog.

Motivation    We need python bindings for this so that we can update the elog
              programattically or through the command line.

How it works  https requests

Dependencies  No pcdshub dependencies
============= ================================================================

External Modules
================
This section will list some of the more site-specific external modules we use
and explain the motivations behind the modules and behind why we use them.

bluesky
-------
============= ================================================================
Repository    `bluesky <https://github.com/nsls-ii/bluesky`_,

How we use it As the scanning, process flow, and automation platform.

Motivation    If we do scanning in a structured way, we can write cleaner code
              that can continue to be maintained, and we can take advantage
              of anything that the ``bluesky`` community has written as far
              as visualizing data and controlling process flow.

How it works  A central ``RunEngine`` object processes user-created
              generators called ``plans``. These can do things like move
              motors, read values, etc., but they can also be inspected to
              see what the plan would do if we ran it, and they can be given
              arbitrary adaptive logic since this is ``Python``.
============= ================================================================

opyhd
-----
============= ================================================================
Repository    `ophyd <https://github.com/nsls-ii/ophyd`_,

How we use it As the device control abstraction layer.

Motivation    We need to have a sane convention for defining devices, and a
              consistent way to define motion interfaces, callbacks, etc.

How it works  ``Device`` subclasses register ``Component`` attributes so that
              every instance knows which PVs to connect to.
============= ================================================================

pydm
----
============= ================================================================
Repository    `pydm <https://github.com/nsls-ii/pydm`_,

How we use it ``pyqt``-based EPICS control screens.

Motivation    We need to be able to include ``Python`` logic in our screens
              for advanced applications, but we also need a way for new users
              to create screens quickly using a drag-and-drop interface.

How it works  This leverages qt ``designer`` as a drag-and-drop interface. A
              rich ``Python`` API is provided for interacting with EPICS PVs
              and with archiver data, among other things. Widgets are provided
              that correspond to the legacy EDM widgets.
============= ================================================================
