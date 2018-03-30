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

   Do not create your own environments here. See the instructions below to
   create your own environment if you want to try different packages

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
