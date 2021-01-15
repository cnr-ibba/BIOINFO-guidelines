
Customize a Pipeline
====================

.. contents:: Table of Contents

Cloning a pipeline
------------------

The easiest way to modifying an existing pipeline is to clone them from the github
repository::

  $ git clone https://github.com/nf-core/rnaseq

.. hint::

  nextflow itself can clone a pipeline like git does::

    $ nextflow clone nextflow-io/rnaseq

.. warning::

  if you clone a pipeline with ``nextflow`` clone, ensure that git *remotes* are
  correct and point to the repository location

Creating a new pipeline
-----------------------

If you can find a proper pipeline in community, you could create a pipeline by your
self. In :ref:`Learning Nextflow <learning-nextflow>` section of these guidelines
you can find a lot of material on working with nextflow. However, the most interesting
feature in nextflow is the `DSL2 <https://www.nextflow.io/docs/latest/dsl2.html>`__
syntax: with it, you can re-use modules in which calculations steps are defined
by the community. In such way, you can avoid to write a full pipeline from yourself.

The minimal set of file required to have a pipeline is to have locally both
``main.nf`` and ``nextflow.config`` inside your project folder: whitout them you
will not be able to add community modules to your pipelines using ``nf-core/tools``::

  $ mkdir my-new-pipeline
  $ cd my-new-pipeline
  $ touch main.nf nextflow.config

.. tip::

  It's a good idea to track your pipeline with a **CVS** software like **git**

.. hint::

  You could also create a new pipeline using the ``nf-core`` template::

    $ nf-core create

  This template is required if you want to submit your pipeline to the ``nf-core`` community.
  Please see the `join the community <https://nf-co.re/developers/adding_pipelines#join-the-community>`__
  section and get in contact with the developers before starting coding with your pipeline

.. _browse-modules-list:

Browsing modules list
~~~~~~~~~~~~~~~~~~~~~

You can get a list of modules by using ``nf-core/tools`` (see :ref:`here <install-nf-core>`
how you can install it)::

  $ nf-core modules list

You could also browse modules inside a different repository and branch, for example::

  $ nf-core modules --repository cnr-ibba/nf-core-modules --branch master list

.. hint::

  In your repositories you can work to a new module and make a pull request to
  add your module to the community. See `nf-core guidelines <https://github.com/nf-core/modules#guidelines>`__
  to understand how you could contribute to the community.

Adding a module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can download and add a module to your pipeline using ``nf-core/tools``::

  $ nf-core modules install . fastqc

.. hint::

  It's possible to specify a different repository and branch from ``nf-core``
  as we did in the :ref:`Browsing modules list <browse-modules-list>`
