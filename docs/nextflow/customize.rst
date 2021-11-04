
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

The minimal set of files required to have a pipeline is to have locally
``main.nf``, ``nextflow.config`` and ``modules.json`` inside your project folder.
You should have also a ``modules`` directory inside your project:: 

  $ mkdir -p my-new-pipeline/modules 
  $ cd my-new-pipeline
  $ touch main.nf nextflow.config modules.json

Next you have to edit modules.json in order to have minimal informations::

  {
    "name": "<your pipeline name>",
    "homePage": "<your pipeline repository URL>",
    "repos": { }
  }


Without this requisites you will not be able to add community modules to your 
pipelines using ``nf-core/tools``.

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

  $ nf-core modules list remote

You could also browse modules inside a different repository and branch, for example::

  $ nf-core modules --github-repository cnr-ibba/nf-modules --branch master list remote

.. hint::

  You can work to a new module and make a pull request to add it to the community. 
  See :ref:`Custom pipeline modules <custom-pipeline-modules>`
  section to work with custom modules. See also 
  `nf-core guidelines <https://nf-co.re/developers/guidelines>`__
  to understand how you could contribute to the community.

.. _adding-a-module-to-a-pipeline:

Adding a module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can download and add a module to your pipeline using ``nf-core/tools``::

  $ nf-core modules install --dir . fastqc

.. note::

  The ``--dir .`` option is optional, the default installation path is the CWD
  (that need to be your pipeline source directory)

.. hint::

  If you don't provide the module, ``nf-core`` will search
  and prompt for for a module in ``nf-core/modules`` GitHub repository

List all modules in a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can have a full list of installed modules using:: 

  $ nf-core modules list local

.. _update-a-pipeline-module:

Update a pipeline module
~~~~~~~~~~~~~~~~~~~~~~~~

You can update a module simple by calling::

  $ nf-core modules update fastqc

.. hint:: 

  Call ``nf-core modules update --help`` to get a list of the available options,
  for example, if you need to install a specific version of a module


Custom pipeline modules
-----------------------

.. _custom-pipeline-modules:

We provide custom DSL2 modules (not implemented by *nf-core* community) in our
repository at `cnr-ibba/nf-modules <https://github.com/cnr-ibba/nf-modules>`__.
This repository is not maintained by *nf-core* community, its internal and intended
to share modules across pipelines and to test stuff locally. It's organized in a
similar way to `nf-core/modules <https://github.com/nf-core/modules>`__, so it's
possible to take a module from here and share it with the *nextflow* community (please see
their `documentation <https://github.com/nf-core/modules#adding-a-new-module-file>`__).
In order to get a list of available custom modules, specify custom modules repository
using ``-g`` parameter (short option for ``--github-repository``), for example::

  $ nf-core modules -g cnr-ibba/nf-modules list remote

.. important::

  `cnr-ibba/nf-modules <https://github.com/cnr-ibba/nf-modules>`__ is a private
  repository (at the moment). In order to browse private repositories with ``nf-core``
  script, you have to configure the `GitHub CLI auth <https://cli.github.com/manual/gh_auth_login>`__::

    $ gh auth login

  and provide here your credentials for **GitHub.com** (using ``https`` as protocol
  an providing a *personal token* with ``repo``, ``read:org``, ``workflow`` scopes
  at least). This *CLI* utility will write the ``$HOME/.config/gh/hosts.yml``
  file with your credentials (please, keep it private!!), which is a requirement
  to satisfy in order to use ``nf-core`` with private repository modules.

Add a custom module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To add a custom module to your pipeline, move into your pipeline folder and call
``nf-core install`` with your custom module repository as parameter, for example::

  $ nf-core modules --repository cnr-ibba/nf-modules install freebayes/single

Create a new module
~~~~~~~~~~~~~~~~~~~

In order to create a new module, clone first the private repository module. Then,
in your local git module repository, create a new module like this::

  $ nf-core modules create freebayes/single --author @bunop --label process_high --meta

.. tip::

  To get more information in creating modules see `Adding a new module <https://nf-co.re/developers/adding_modules>`__
  guide.

Testing a new module
~~~~~~~~~~~~~~~~~~~~

The custom repository module is configured to use *GitHub WorkFlows* in order to perform
some tests on all modules. Please, try to define tests and configuration files like other
modules (you can take a look to community modules to get some examples). You can try to
test some modules locally before submitting a **pull request** to the custom repository
modules. The python package ``pytest-workflow`` is a requirement to make such tests.
You need also to specify an environment between ``conda``, ``docker`` or ``singularity``
in order to perform test. Use tags to specify which tests need to be run::

  $ NF_CORE_MODULES_TEST=1 PROFILE=docker pytest --tag freebayes/single --symlink --keep-workflow-wd

You need to check also syntax with ``nf-core`` script by specify which tests to call
using *tags*::

  $ nf-core modules lint freebayes/single

If you are successful in both tests, you have an higher chance that your tests will
be executed without errors in GitHub workflow.

Subworkflows
------------

A subworkflow is an experimental feature which allow to include a chain of modules 
together (for example ``bam_sort_samtools``, which execute *samtools sort*, *samtools
index* and then call the ``bam_stats_samtools``, which is another subworkflow. 
There are imported in the main workflow (pipeline) like any others modules. More 
information will be added in future.

Pipeline best practices
-----------------------

Use DSL2 syntax when possible
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**DSL2** is the newest pipeline standard and the nextflow community is currently 
moving to this format. This means that community pipelines will be updated to fully 
support this standard and if you plan to submit your pipeline to the community
you will probably need to write code using this format. 

The major changes provided by **DSL2** format are *modules*, as described 
by this docs, which let you reuse softwares managed and provided by the community 
simplifying your pipeline: the code required to run software and to provide/collect 
input and output are provided by the modules, which can be :ref:`installed <adding-a-module-to-a-pipeline>` or 
:ref:`updated <update-a-pipeline-module>` as described by this guide.

Another change introduced in **DSL2** is the different way you can pass data between
different pipeline steps. With the old standard, the only way is by using channels:
this implies that after consuming values from a channel you cannot reuse those values
in another pipeline step. For example if one step produces and output required 
by two or more steps, you have to put data in two or more channels, like this::

  output:
  file '*.fq' into trimmed_reads, quantifier_input_reads

and once ``trimmed_reads`` values are consumed, you cannot read these values in 
another step. Another example could be a step in which 
you align reads to an indexed genome made by a different step: since the genome 
index is emitted once from the indexing step, you will be able to align only one
sample if you pass the channels as they are in input: the only way to align all 
your samples is to use the 
`combine operator <https://www.nextflow.io/docs/latest/operator.html#combine>`__
and put all values in a new channel:: 

  trimmed_reads.combine(genome_index).set{ align_input }

and then read those values as a tuple:: 

  input:
  tuple file(sample), file(genome) from align_input

In the newest **DSL2** version, you can specify the *output* values from the 
module itself without using the channels syntax, for example::

  BWA_MEM(TRIMGALORE.out.reads, BWA_INDEX.out.index)

and values from a module step can be read as many times as needed.

.. warning:: 

  ``set`` and ``into`` operators used in previous version are removed in **DSL2**.
  See `DSL 2 <https://www.nextflow.io/docs/latest/dsl2.html>`__ nextflow documentation
  to have a picture of major changes.

Write the configuration stuff outside your pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Since the aim of nextflow pipelines is reproducibility and portability,
you should avoid to place your *analysis specific parameters* in your pipeline main 
script: this force users to modify your pipeline according their needs and this 
implies different pipeline scripts with differ only for a few things, for example 
where the input files are. If you place your configuration files outside your main 
script, you can re-use the same parameters within different scripts and keep 
your main file unmodified: this keeps the stuff simple and let you to focus only 
on important changes with your *CVS*. For example, you could define a ``custom.config``
*JSON* in which specify your specific requirements:: 

  params {
    // Input / Output parameters
    readPaths = "$baseDir/fastq/*.fastq.gz"
    outdir = "results"

    // reference genome
    genome = "/path/to/genome.fasta"
  }

An then calling nextflow by providing your custom parameters::

  $ nextflow run -resume main.nf -c custom.config --profile singularity

Moreover, by writing specific configuration parameters let you to call a remote 
pipeline with ``nextflow run`` without collect nextflow code in your analysis directory.

.. hint:: 

  nextflow looks for configurations in different locations, and each location is 
  ranked in order to decide which settings will be applied: you can override the 
  default configuration by using a configuration source with an higher priority, 
  for example the ``-c <config file>``, ``-params-file <file>`` or parameters 
  provided with command line are different locations where the last have the higher priority. See 
  `Configuration file <https://www.nextflow.io/docs/latest/config.html#configuration-file>`__
  section of nextflow documentation.

Add test data to your pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It frustrating writing a pipeline on a real dataset: steps could require a lot 
of time to be completed and if you made any errors when calling software or when 
collecting outputs you will be noticed after a long period of time and you have 
no way to recover the data you have with a nextflow error. 
In *testing* and *revision* stages or when adding new features, consider 
to work with a *reference data sets* like the 
one provided by `nextflow community <https://github.com/nf-core/test-datasets>`__
or add some public data to your pipeline. Please, remember to not track big files 
with your CVS: you should provide the minimal requirements to get your pipeline
running as intended in the shortest time. You should also consider 
to provide a ``test`` profile with the required parameters which let you to test 
your pipeline like this:: 

  $ nextflow run . -profile test,singularity

Where the ``test`` profile is specified in ``nextflow.config`` and refers to 
the *test dataset* you provide with your pipeline:: 

  profiles {
    ...

    test {
      // test input reads
      reads_path = "./testdata/GSE110004/*{1,2}.fastq.gz"

      // Genome references
      genome_path = "./testdata/genome.fa"
    }
  }

This type of test could be used even with CI system, like 
`GitHub workflow <https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions>`__.
