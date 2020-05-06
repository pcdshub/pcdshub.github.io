=======================
PCDS Python Style Guide
=======================
As much as possible, PCDS code should be written with the following guidelines
in mind. For examples, please look at the :ref:`Example Module`.

Automatic style checking
========================
We currently use the following tools to automatically check our code. Please
consider adding them to your workflow.

* Use `pre-commit <https://pre-commit.com>`_ to run a series of checks already
  specified for a repository. Look `here
  <https://github.com/pcdshub/pre-commit-hooks>`__ for more information on how
  to set it up with our workflow.
* `flake8 <https://flake8.pycqa.org>`_ is a useful tool that checks both style
  and coding errors. It should be run as part of any Python project's
  pre-commit configuration and is part of our CI workflow (i.e. PRs need to
  pass flake8 to be merged).
* We are also currently exploring the possibility of using `pydocstyle
  <http://www.pydocstyle.org>`_ for docstring checking.

Code
====
Our code follows `PEP-8`_ guidelines. Please look there for any code style
matters. Since this is a very widely used standard, Google is also very helpful
if you have any questions. To standardize our convention for strings, please
use single quotes except for when the string itself contains single quotes.

Docstrings
==========
Please write docstrings for all modules, classes, and functions/methods. In
general, our docstrings should mostly follow `PEP-257`_ with the exception
of an allowance for 2-line docstrings.

General Rules
-------------
* Always use ``"""triple double quotes"""`` for docstrings.
* End all summaries/descriptions with periods.
* Built-in types (like str and int) should not be quoted or backticked.
* Numeric values should be left unbackticked (e.g. ``1``).
* Strings should be quoted (e.g. ``'foo'``).
* Built-in constants and keywords can be emphasized with backticks. An
  appropriate reference to python docs with be automatically generated
  (e.g. ```True``` or ```assert```).
* args and kwargs should be noted without asterisks (e.g. ``kwargs`` not
  ``**kwargs``).
* Please use single backticks around all class/function/module/attribute names
  so that the appropriate documentation can be properly cross-referenced.
  Please check that the link is made properly. In some cases, you may need to
  specify the role manually (e.g. ``:meth:`__init__```) and/or include the
  parent (e.g. ```InOutPositioner.remove```).

Module Rules
------------
Modules should have a brief summary of what is defined within it.
The quotes should be on their own lines with no blank lines within or following
the docstring. For example:

.. code:: python

    """
    Module for goniometers and sample stages used with them.
    """
    from ophyd import Device

If you're feeling extra helpful or the module is on the more complicated side,
please give a longer description below the summary, separated by a blank line.

.. code:: python

    """
    Module to centralize code related to devices that can be 'IN' or 'OUT'.

    The classes that are defined here can be used to create other devices that need
    methods such as :meth:`~InOutPositioner.insert` or
    :meth:`~InOutPositioner.remove` and the ability to mark discrete states as in
    the beam or out of the beam.
    """
    import math

Classes, Methods, and Functions
-------------------------------
If they are not intended to be called by a user, a 1-liner will do. Otherwise,
please also include sections as specified by `numpydoc`_.

One-liner Docstrings
^^^^^^^^^^^^^^^^^^^^
`PEP-257`_ says "One-liners are for really obvious cases." In these cases, the
quotes should be on the same line with no extra whitespace surrounding the
docstring. Code should begin on the following line. For example:

.. code:: python

    class FeeFilter(InOutPositioner):
        """A single attenuation blade, as implemented in the FEE."""
        state = Cpt(EpicsSignal, ':STATE', write_pv=':CMD')

Two-liner Docstrings
^^^^^^^^^^^^^^^^^^^^
Sometimes, a class or function is simple enough to warrant a one-liner but the
docstring won't fit within `PEP-8`_'s 79 character limit. In this case, the
quotes may be moved to separate lines, as shown below:

.. code:: python

    def screen(self):
        """
        Opens Epics motor expert screen e.g. for reseting motor after stalling.
        """
        executable = 'motor-expert-screen'

If the description still does not fit, you may use two lines:

.. code:: python

    def get_raw_mesh_voltage(self):
        """
        Get the current acromag voltage that outputs to the HV supply, i.e
        the voltage seen by the HV supply.
        """
        return self.read_sig.get()

Multi-line Docstrings
^^^^^^^^^^^^^^^^^^^^^
When a short summary is not sufficient, multi-line docstrings must be used.
In these cases, an additional description can be given following the summary,
separated by a blank line. Furthermore, please embellish this documentation
with sections like Parameters or Attributes as specified by the `numpydoc`_
standard.

Some PCDS-specific rules for these docstrings:

* Triple quotes should be on their own lines.
* The docstring should be followed by a blank line.
* The summary must be single line. If further explanation is necessary, move it
  to the description, which can be as long as you want.
* Class parameters should be described in the class's docstring under the
  Parameters section. Therefore, the :meth:`__init__` method can be blank.

.. note::
    In listed sections like Parameters or Attributes, the colon must be
    preceded by a space, or omitted if the type is absent. Also, the type
    should not be backticked, even if it's a custom object; the reference will
    be made anyway.

.. code::

    def collect_prefixes(cls, device, kwargs):
        """
        Gather all the special prefixes from a device's kwargs.

        This must be called once during the ``__init__`` of a device with
        UnrelatedComponent instances.

        Parameters
        ----------
        device : ~ophyd.device.Device
            The device to gather prefixes for. Typically this is just ``self``.

        kwargs : dict
            The kwargs dictionary with extra prefixes defined.
        """

        device.unrelated_prefixes = {}

Extra Notes
-----------
* Physical units should be specified in a parameter's description, not with its
  type.
* Inline code blocks can be specified with double-backticks
  (e.g. ````return 0````).
* Treat PV names as strings, surrounding them with single-quotes
  (e.g. ``'CXI:JET:X'``).
* Capitalize the first letter of a parameter's description.
* Prepending a backticked dotted name with a tilde(~) will display only the
  lowest-level of the name, while still using the full name for the
  cross-referencing link. For example, ``:exc:`~ophyd.utils.LimitError``` will
  be displayed as :exc:`~ophyd.utils.LimitError`.

External Guides
===============
* `PEP-8 <https://www.python.org/dev/peps/pep-0008>`_ -- Official Style Guide for Python Code
* `PEP-257 <https://www.python.org/dev/peps/pep-0257>`_ -- Official conventions for Python docstrings
* `numpydoc <https://numpydoc.readthedocs.io/en/latest/format.html>`_ -- Useful for rules about sections
* `sphinx <http://www.sphinx-doc.org/en/master/usage/restructuredtext/domains.html#the-python-domain>`_ -- Generally helpful for working with sphinx directives
* `Example Project <https://pythonhosted.org/an_example_pypi_project/sphinx.html>`_ -- Some useful examples

Acknowledgements
================
The docstring guidelines are derived/adapted from the `numpydoc`_ docstring
guide and `PEP-257`_ guidelines. numpydoc is Copyright © 2019, numpydoc
maintainers.
