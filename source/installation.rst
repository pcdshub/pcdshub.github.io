============
Installation
============
PCDS uses Conda to manage Python packaging. This allows users to quickly
assemble virtual environments with different combinations of packages. An
elementary understanding of what Conda is, and how to use it is assumed in the
tutorial below. If you need a refresher, `here
<https://conda.io/docs/user-guide/getting-started.html>`_ is a good place to start. 

Using an Existing Environment
=============================
A Conda deployment is maintained in ``/reg/g/pcds/pyps/conda`` with different
versions of the ``pcds`` environment. Activate the latest one by:

.. code:: bash

   $ source /reg/g/pcds/pyps/conda/py36env.sh

If you want an older release, enter this as an argument to the shell script.
The environment contained is **read-only**. You should not try and add packages
to these environments. If you believe that a package should be included that is
not present, contact PCDS and we will include it a subsequent release.

.. note::

   Do not create your own environments here.
   See :ref:`ref-create-env` if you want to try different packages

Using In-Development Packages
=============================
It is possible to develop new packages without creating your own environment.
This is useful if you made changes to a library or multiple libraries and want
to test how they interact with eachother and with the rest of the released
ecosystem. The recommended way to do this is to use ``$PYTHONPATH`` to mask the
shared packages with your in-development packages.

Tools are provided in our
`Engineering Tools <https://github.com/pcdshub/engineering_tools>`_ repo
to keep this process manageable. You can stay up-to-date with the most recent
tools releases by keeping
``/reg/g/pcds/engineering_tools/engineering_tools/scripts``
on your path, or by cloning the github repository.
You can also use these scripts as a starting point for your own.
The relevant scripts are:

- ``pydev_env``: Source this to activate your development environment.
- ``pydev_register <path> <module or bin>``:
  Use this to set up your development environment.

These scripts work by:

- Activating the shared environment
- prepending ``$PYTHONPATH`` with ``~/pydev``
- prepending ``$PATH`` with ``~/pydev/bin``
- filling ``~/pydev`` and ``~/pydev/bin`` with softlinks to your checked-out
  packages and scripts

See :doc:`development` for instructions on checking out and modifying packages.
See below for examples on using these scripts.

.. note::

   You can use this in a hutch-python instance too! Just add
   ``source pydev_env`` to the end of the ``hutchnameversion`` file.
   Warning: Do not check this in or include it in a release, or each user may
   have slightly different startup behavior...

.. code:: bash

   # Local repository checkouts
   $ ls
   happi hutch-python pcdsdevices special_package

   # Add the modules to our PYTHONPATH
   $ pydev_register happi/happi module
   $ pydev_register hutch-python/hutch_python module
   $ pydev_register pcdsdevices/pcdsdevices module
   $ pydev_register special_package/special_package module

   # The module softlinks are created here
   $ ls ~/pydev
   bin happi hutch_python pcdsdevices special_package

   # Add the hutch-python script to our PATH
   $ pydev_register hutch-python/bin/hutch-python bin

   # The bin softlinks are created here
   $ ls ~/pydev/bin
   hutch-python

   # Activate our environment
   $ source pydev_env
   $ ipython

.. ipython::
   :verbatim:

   In [1]: import special_package

   In [2]: import pcdsdaq

   In [3]: import pcdsdevices

   In [4]: pcdsdaq.__file__
   Out[4]: '/reg/g/pcds/pyps/conda/py36/envs/pcds-1.0.0/lib/python3.6/site-packages/pcdsdaq/__init__.py'

   In [5]: pcdsdevices.__file__
   Out[5]: '/reg/neh/home/username/pydev/pcdsdevices/__init__.py'

.. code:: bash

   # Some time later: our PRs are done, clear our development path
   $ rm ~/pydev/*
   $ rm ~/pydev/bin/*


.. _ref-create-env:

Creating Your Own Environment
=============================
Many developers may want to create their own environments to experiment with
different packages and tools. We recommend that you do this in your own
Miniconda installation. From a machine with internet access:

.. code:: bash

   $ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh;
  
   $ bash miniconda.sh -b -p ~/miniconda

This will give you a clean installation of Conda for you to play around with.
Feel free to create and name environments as you please. You can make
conda ready to use by sourcing the following scripts. You may want to
include this in your ``.bashrc`` or startup file equivalent:

.. code:: bash

   $ source ~/miniconda/etc/profile.d/conda.sh

or, for tcsh:

.. code:: tcsh

   $ source ~/miniconda/etc/profile.d/conda.csh

If you want to create a copy of the latest PCDS deployment environment the
easiest way is to use the ``.yaml`` specification that we keep with the main
Conda deployment.

.. code:: bash

   $ conda env create -n myenvname -f /reg/g/pcds/pyps/conda/pcds-envs/pcds.yaml

This will create an environment ``myenvname`` that is an exact copy of the
deployment environment in your own Conda installation.

You can activate or deactivate an environment with the following commands:

.. code:: bash

   $ conda activate myenvname
   $ conda deactivate
