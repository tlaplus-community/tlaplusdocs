.. _spec-file-layout:

++++++++++++++++++
Spec File Layout
++++++++++++++++++

File Type
=========

TLA+ spec files use the ``.tla`` file type suffix.

.. _modules:

Modules
=======

A TLA+ spec file usually contains a single module, although putting multiple modules in a single file is supported.
The top-level (first) module in the file must have the same name as the file itself.
For example, in file ``MySpec.tla`` we can define the top-level module as follows::

    ---- MODULE MySpec ----

    ====

The first line of the module definition must be surrounded by at least four ``-`` symbols with the ``MODULE`` keyword, and the last line of the definition must consist of at least four ``=`` symbols.
Usually these are padded so both lines are 78 characters long.
All TLA+ definitions must be between these two lines or they are ignored.
The module name (and by extension the file name) is restricted to alphanumeric characters plus underscores; no other symbols can be used.
The name requires a minimum length of one character and has no specified maximum length.

Multiple Modules
================

Define multiple modules in a single file as follows::

    ---- MODULE MySpec ----

    ====

    ---- MODULE SecondModule ----

    ====

    ---- MODULE ThirdModule ----
    
    ====

The top-level (first) module in the file must have the same name as the file itself; the following modules have no such name restrictions.
Modules within a single file can import definitions using ``EXTENDS`` or ``INSTANCE`` from any of the others in the file regardless of order, although these dependencies cannot be circular.

Free-Form Text
==============

Text outside of module definitions is ignored, even if it isn't in comment form; this can be useful for documentation purposes.
For example::

    This text is ignored.

    ---- MODULE MySpec ----

    ====

    This text is also ignored.

    ---- MODULE SecondModule ----

    ====

    This text, too, is ignored.

    ---- MODULE ThirdModule ----
 
    ====

    Text here is further ignored.

The text is preserved when compiling the spec file into LaTeX.
