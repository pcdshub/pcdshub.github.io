Common Questions and Issues
---------------------------

.. contents::
   :depth: 1
   :local:

IPython and Matplotlib
######################
Many of the applications launched in an ``IPython`` session will try and create
graphs using ``matplotlib``. This works well in principle, but unfortunately
requires a little bit of configuration ahead of time. Even more unfortunate is
the fact that issues rarely have tracebacks or error messages to follow.

If you are having troubles creating plots on a machine that is not your own,
the first thing to check is that you have X-11 forwarding enabled. The easiest
way to do this is make sure you always log in with the ``ssh -X`` or ``ssh -Y``
options. You can also check the environment variable ``$DISPLAY`` which should
return a non-empty value:

In bash:

.. code:: shell

   echo $DISPLAY

or in the ``IPython`` shell itself:

.. code:: python

   import os
   print(os.getenv('DISPLAY'))

If that isn't the issue you should check that you have configured
``matplotlib`` to use ``Qt`` as the backend for all generated plots. This is
done automatically by the ``hutch-python`` start-up scripts, but if you are
entering an ``IPython`` session of your own, you should configure the backend
by typing:

.. code:: python

   %matplotlib qt

Finally if you are seeing the created plot but it doesn't update during your
Bluesky scan, you need to configure a kicker. This is installed in the
``RunEngine`` event loop that updates your plots when it isn't busy executing
your scan. Once again, this is configured for you in the ``hutch_python``
``IPython`` shell, but if you are using a basic ``IPython`` shell this is an
important step.

.. code:: python

   from bluesky.utils import install_kicker
   install_kicker()
