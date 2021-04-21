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

A TLA+ spec file contains a single module, although additional modules can be nested within the top-level module.
The top-level module must have the same name as the file itself.
For example, in file ``MySpec.tla`` we can define the top-level module as follows::

    ---- MODULE MySpec ----

    ====

The first line of the module definition must be surrounded by at least four ``-`` symbols with the ``MODULE`` keyword, and the last line of the definition must consist of at least four ``=`` symbols.
Usually these are padded so both lines are 78 characters long.
All TLA+ definitions must be between these two lines or they are ignored.
The module name (and by extension the file name) has the following restrictions:

 * Name can consist only of alphanumeric characters and underscores

 * Name has minimum length of one character with no specified maximum length

 * If name is only one character long, that character must be a letter

 * Name cannot start with the prefix "WF\_" or "SF\_"

Extends
=======

If the module extends any additional TLA+ modules, this must be specified with an ``EXTENDS`` statement prior to any other statement except for comments::

    ---- MODULE MySpec ----
    EXTENDS Naturals, FiniteSets, Sequences

    ====

The list of modules to extend are given as a comma-separated list.
Only one ``EXTENDS`` statement can be present in each module.

Nested Modules
================

Define multiple nested modules in a single file as follows::

    ---- MODULE MySpec ----
    ---- MODULE FirstTierNested ----
    ---- MODULE SecondTierNested ----
    
    ====
    ====

    INSTANCE FirstTierNested

    ---- MODULE AnotherModule ----
    
    ====

    INSTANCE AnotherModule
    ====

The top-level module in the file must have the same name as the file itself; the nested modules have no such name restrictions.
Outer modules can only refer to nested modules after they are defined, which means outer modules cannot import nested modules with ``EXTENDS`` and instead must use ``INSTANCE``.
Modules cannot directly refer to a module nested inside another module.
In the above example, ``MySpec`` cannot refer directly to ``SecondTierNested``.

Free-Form Text
==============

Text outside of the top-level module definition is ignored, even if it isn't in comment form; this can be useful for documentation purposes.
For example::

    This text is ignored.

    ---- MODULE MySpec ----

    ====

    This text is also ignored.

The text is preserved when compiling the spec file into LaTeX.

Formal Grammar
==============

The BNF grammar rule for modules is as follows::

    G.Module ⩴
        AtLeast4("-") & tok("MODULE") & Name & AtLeast4("-")
        & (Nil | (tok("EXTENDS") & CommaList(Name)))
        & (G.Unit)^*
        & AtLeast4("=")
