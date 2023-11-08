
Getting started
===============

.. contents:: Table of Contents

Introduction
------------

Nextflow is a bioinformatics workflow manager in which we can develop or reuse
bioinformatics pipeline analyses. It manages and supports many execution platforms
like local, HPC, cloud and so one. The aim is to develop a pipeline which can work
locally on your laptop and eventually in HPC environments or any other resource
that could scale with your data. This could be achieved by writing pipelines in
`Nextflow scripting <https://www.nextflow.io/docs/latest/script.html>`__ language
(or `DSL2 <https://www.nextflow.io/docs/latest/dsl2.html>`__) and managing your
software requirements with :doc:`conda <../general/conda>`,
:doc:`singularity <../general/singularity>` or :doc:`docker <../general/docker>`.

.. _learning-nextflow:

Learning Nextflow
~~~~~~~~~~~~~~~~~

There are online a series of resources and tutorial about nextflow. The first is
`this youtube playlist <https://www.youtube.com/watch?v=8_i8Tn335X0&list=PLPZ8WHdZGxmUv4W8ZRlmstkZwhb_fencI&ab_channel=Nextflow>`__
(here are `the tutorial notes <https://seqera.io/training/>`__ to code along and
a `git repository <https://github.com/bunop/nextflow-training>`__ adapted to work in a local environment).
Next there is the nextflow `reference documentation <https://www.nextflow.io/docs/latest/basic.html>`__,
which explains things in details. The community builded pipelines can be found
in the `pipeline section <https://nf-co.re/pipelines>`__ of `nf-core <https://nf-co.re/>`__
community site, while DSL2 pipeline modules can be found in `nf-core/modules <https://github.com/nf-core/modules>`__
github repository.

Installing Nextflow
-------------------

In order to install nextflow in your local environment ensure you have java installed::

  $ java -version

Next you could install nextflow in your local directory with::

  $ curl -s https://get.nextflow.io | bash

If you install nextflow in a directory inside your ``$PATH`` environment, you can
avoid to specify the relative or the full path when calling nextflow. Verify your
installation with::

  $ mkdir nf-hello
  $ cd nf-hello
  $ nextflow run hello

.. hint::

  Nextflow is already installed in our shared **core** environment, and can be called
  like a command since is available via ``$PATH`` environment variable

.. _install-nf-core:

Install nf-core/tools
~~~~~~~~~~~~~~~~~~~~~

`nf-core/tools <https://github.com/nf-core/tools>`__ is a python package which
integrates nextflow and is an helper tools for the nextflow community. Using
``nf-core`` software you could manage nextflow pipelines and modules. You can install
``nf-core`` in `many ways <https://github.com/nf-core/tools#installation>`__,
but the recommended way is using pip::

  $ pip install nf-core
  $ nf-core --help

.. note::

  You could install ``nf-core`` in a conda environment. Even if there's a ``nf-core``
  conda package, is better to install the **pypi** package version since it is the
  most update release and avoid some dependency issues with **bioconda**::

    $ conda create --name nf-core pip
    $ conda activate nf-core
    $ pip install nf-core

  An alternative way to install nf-core (v14) with conda is by installing package
  from both ``bioconda`` and ``conda-forge`` channels::

    $ conda create --channel bioconda --channel conda-forge --name nf-core nf-core=1.14

  However, it's better to do this in a fresh conda environment used only for nextflow.
  Please see our consideration :ref:`on channels <a-note-on-channels>`.

.. hint::

  ``nf-core`` is already installed in a ``nf-core`` environment in our shared **core**
  *VM*::

    $ source activate nf-core
    $ nf-core --help

.. _configuring_nextflow:

Configuring nextflow
--------------------

Nextflow can be customized in different ways: there are configuration files,
which can be used to customize a single pipeline execution, and environment
variables, which can be used to customize the Nextflow runtime and the underlying
Java virtual machine. There's also a ``$HOME/.nextflow/config`` file which can
be used to customize the default configuration of nextflow, for example by limiting
resources usage::

  executor {
    name = 'slurm'
    queueSize = 50
    submitRateLimit = '10 sec'
  }

In this way is possible to setup a default configuration for all your pipelines,
by limiting the job submission in order to avoid to overload the cluster scheduler.
Nextflow configuration files are stored in multiple locations, and are loaded in
different order. This means that you can have a default configuration file in
``$HOME/.nextflow/config`` and a pipeline specific configuration file in the
pipeline directory, and the latter will override the former. You could find more
information in the `nextflow documentation <https://www.nextflow.io/docs/latest/config.html#configuration-file>`__.
There are some tips for HPC users, please take a look at nextflow forum for
`5 Nextflow Tips for HPC Users <https://www.nextflow.io/blog/2021/5_tips_for_hpc_users.html>`__
and `Five more tips for Nextflow user on HPC <https://www.nextflow.io/blog/2021/5-more-tips-for-nextflow-user-on-hpc.html>`__
articles.

.. _set-singularity-cache:

Setting ``NXF_SINGULARITY_CACHEDIR``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using nextflow with singularity lets you to define a directory where remote Singularity
images are stored. This could speed up **a lot** pipelines execution times, since images
are downloaded once and then used when needed. You can define the location of such
directory by setting the ``NXF_SINGULARITY_CACHEDIR`` environment variable. Nextflow
will create such directory for you and will place every singularity downloaded image
inside this directory

.. hint::

  ``NXF_SINGULARITY_CACHEDIR`` is already defined for every user in our shared **core**
  infrastructure, and points by default at your ``${HOME}/nxf_singularity_cache/`` directory.
  If you want to change this value (for example, by setting a shared cache folder),
  you have to define such variable in your ``$HOME/.profile`` configuration file,
  for example::

    # override nextflow singularity cache dir
    export NXF_SINGULARITY_CACHEDIR=/home/core/nxf_singularity_cache/

.. warning::

  When using a computing cluster it must be a shared folder accessible from all computing nodes.

.. _nextflow_environment_variables:

Other nextflow environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are others environment variables which could be useful to set in order to
customize your nextflow experience. You could find a list of them in the
`nextflow documentation <https://www.nextflow.io/docs/latest/config.html#environment-variables>`__.
Here are a selection of them:

.. list-table:: Nextflow environment variables
   :header-rows: 1
   :widths: 25 50 25

   *  - Name
      - Description
      - Example
   *  - NXF_EXECUTOR
      - Defines the default process executor
      - ``slurm``
   *  - NXF_OPTS
      - | Provides extra options for the Java and Nextflow runtime.
        | It must be a blank separated list of ``-Dkey[=value]`` properties
      - ``-Xms500M -Xmx2G``
   *  - NXF_SINGULARITY_CACHEDIR
      - | Directory where remote Singularity images are stored.
        | When using a computing cluster it must be a shared
        | folder accessible from all compute nodes.
      - ``$WORK/nxf_singularity_cache``
   *  - NXF_WORK
      - | Directory where working files are stored
        | (usually your scratch directory)
      - ``"$CINECA_SCRATCH/nxf_work"``
   *  - NXF_OFFLINE
      - | When true disables the project automatic download and
        | update from remote repositories (default: ``false``).
      - ``true``
   *  - NXF_ANSI_LOG
      - | Enables/disables ANSI console output
        | (default ``true`` when ANSI terminal is detected).
      - ``false``

Those environment variables could be set in your ``$HOME/.profile`` (Debian) or
``$HOME/.bash_profile`` (Red-Hat) configuration files, for example:

.. code-block:: bash

  # Nextflow custom environment variables
  export NXF_EXECUTOR=slurm
  export NXF_OPTS="-Xms500M -Xmx2G"
  export NXF_SINGULARITY_CACHEDIR="$WORK/nxf_singularity_cache"
  export NXF_WORK="$CINECA_SCRATCH/nxf_work"
  export NXF_OFFLINE='true'
  export NXF_ANSI_LOG='false'

.. _nextflow-private-repo:

Access to private repositories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The file ``$HOME/.nextflow/scm`` can store the configuration required to access to
private repository in GitHub, for example::

  providers {
    github {
      user = '<your GitHub user>'
      password = '<your GitHub password>'
    }
  }

You could find more information in
`SCM configuration file <https://www.nextflow.io/docs/latest/sharing.html?highlight=credentials#scm-configuration-file>`__
section of nextflow documentation.

Access to private nextflow modules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to get access to the private
`nextflow-modules <https://github.com/cnr-ibba/nf-modules>`__, you need to
configure `GitHub CLI <https://cli.github.com/>`__ in order to create the
``~/.config/gh/hosts.yml`` file, which is a fundamental requisite in order to
deal with private modules with ``nf-core modules``.
The easiest way to create this configuration is through *GitHub CLI*::

  gh auth login

See the documentation on `gh auth login <https://cli.github.com/manual/gh_auth_login>`__
to have more information
