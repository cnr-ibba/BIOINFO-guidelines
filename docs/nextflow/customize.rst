
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

  if you clone a pipeline with ``nextflow clone`` command, ensure that git *remotes* are
  correct and point to the repository location

Creating a new pipeline
-----------------------

If you can't find a proper pipeline in community, you could create a pipeline by your
self. In :ref:`Learning Nextflow <learning-nextflow>` section of these guidelines
you can find a lot of material on working with nextflow. However, the most interesting
feature in nextflow is the `DSL2 <https://www.nextflow.io/docs/latest/dsl2.html>`__
syntax: with it, you can re-use modules in which calculations steps are defined
by the community. In such way, you can avoid to write a full pipeline from yourself.

The minimal set of files required to have a pipeline is to have locally both
``main.nf`` and ``nextflow.config`` inside your project folder: without them you
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

  $ nf-core modules --repository cnr-ibba/nf-modules --branch master list

.. hint::

  In your repositories you can work to a new module and make a pull request to
  add your module to the community. See :ref:`Custom pipeline modules <custom-pipeline-modules>`
  section to work with custom modules. See also `nf-core guidelines <https://github.com/nf-core/modules#guidelines>`__
  to understand how you could contribute to the community.

Adding a module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can download and add a module to your pipeline using ``nf-core/tools``::

  $ nf-core modules install . --tool fastqc

.. note::

  the ``.`` directory is required to install the module in your pipeline folder

.. hint::

  if you don't provide the module with ``--tool`` option, ``nf-core`` will search
  and prompt for for a module in ``nf-core/modules`` GitHub repository

Custom pipeline modules
-----------------------

.. _custom-pipeline-modules:

We provide custom DSL2 modules (not implemented by *nf-core* community) in our
repository at `cnr-ibba/nf-modules <https://github.com/cnr-ibba/nf-modules>`__.
This repository is not maintained by *nf-core* community, its internal and intended
to share modules across pipelines and to test stuff locally. It's organized in a
similar way to `nf-core/modules <https://github.com/nf-core/modules>`__, so it's
possible to take a module and share it with the *nextflow* community (please see
their `documentation <https://github.com/nf-core/modules#adding-a-new-module-file>`__).
In order to get a list of available custom modules, specify custom modules repository
using ``-r`` parameter, for example::

  $ nf-core modules -r cnr-ibba/nf-modules list

.. important::

  `cnr-ibba/nf-modules <https://github.com/cnr-ibba/nf-modules>`__ is a private
  repository (at the moment). In order to browse private repositories with ``nf-core``
  script, you have to configure the `GitHub CLI auth <https://cli.github.com/manual/gh_auth_login>`__::

    $ gh auth login

  An provide here your credentials for **GitHub.com** (using ``https`` as protocol
  an providing a *personal token* with ``repo``, ``read:org``, ``workflow`` scopes
  at least). This *CLI* utility will write the ``$HOME/.config/gh/hosts.yml``
  file with your credentials (please, keep it private!!), which is a requirement
  to satisfy in order to use ``nf-core`` with private repository modules.

Add a custom module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To add a custom module to your pipeline, move into your pipeline folder and call
``nf-core install`` with your custom module repository as parameter, for example::

  $ nf-core modules --repository cnr-ibba/nf-modules install . --tool freebayes/single

.. note::

  the ``.`` directory is required to install the module in your pipeline folder

Create a new module
~~~~~~~~~~~~~~~~~~~

In order to create a new module, clone first the private repository module. Then,
in your local git module repository, create a new module like this::

  $ nf-core modules create . --tool freebayes --author @bunop --label process_high --meta

.. note::

  The ``nf-core`` is yet simple at this moment, if you need to create a module with
  the same prefix, like ``freebayes/single`` or ``freebayes/multi``, simply create
  the base module (ie ``freebayes``) then copy and move stuff in subfolders.

.. tip::

  See `nf-core/README.md <https://github.com/nf-core/modules/blob/master/README.md>`__
  to get a full list of available options.

Testing a new module
~~~~~~~~~~~~~~~~~~~~

The custom repository module is configured to use *GitHub WorkFlows* in order to perform
some tests on all modules. Please, try to define tests and configuration files like other
modules (you can take a look to community modules to get some examples). You can try to
test some modules locally before submitting a **pull request** to the custom repository
modules. The python package ``pytest-workflow`` is a requirement to make such tests.
You need also to specify an environment between ``conda``, ``docker`` or ``singularity``
in order to perform test. Use tags to specify which tests need to be run::

  $ PROFILE=docker pytest --tag freebayes/single --symlink --keep-workflow-wd

You need to check also syntax with ``nf-core`` script by specify which tests to call
using *tags*::

  $ nf-core modules lint . -t freebayes/single

If you are successful in both tests, you have an higher chance that your tests will
be executed without errors in GitHub workflow.
