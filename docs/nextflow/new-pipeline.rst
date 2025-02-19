
Create a new pipeline
=====================

.. contents:: Table of contents

Start from scratch
------------------

If you can't find a proper pipeline in community, you could create a pipeline by your
self. In :ref:`Learning Nextflow <learning-nextflow>` section of these guidelines
you can find a lot of material on working with nextflow. However, the most interesting
feature in nextflow is the `DSL2 <https://www.nextflow.io/docs/latest/dsl2.html>`_
syntax: with it, you can re-use modules in which calculations steps are defined
by the community. In such way, you can avoid to write a full pipeline from yourself.

The minimal set of files required to have a pipeline is to have locally
``main.nf``, ``nextflow.config`` and ``modules.json`` inside your project folder.
You should have also a ``modules`` directory inside your project::

  mkdir -p my-new-pipeline/modules
  cd my-new-pipeline
  touch main.nf nextflow.config modules.json README.md .nf-core.yml

Next you have to edit ``modules.json`` in order to have minimal information:

.. code-block:: json

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

    nf-core pipelines create

  This utility command will configure a pipeline to be submitted to the ``nf-core``
  community or let you to customize all the options to include in a pipeline that
  can be kept private or stand-alone (not to be submitted to the community).
  Please see the `join the community <https://nf-co.re/docs/tutorials/adding_a_pipeline/overview#join-the-community>`_
  section and get in contact with the developers if you plan to contribute to the
  community.

.. _browse-modules-list:

Browsing modules list
~~~~~~~~~~~~~~~~~~~~~

You can get a list of modules by using ``nf-core/tools`` (see :ref:`here <install-nf-core>`
how you can install it)::

  nf-core modules list remote

You could also browse modules inside a different repository and branch, for example::

  nf-core modules --github-repository https://github.com/cnr-ibba/nf-modules.git \
    --branch master list remote

.. hint::

  You can work to a new module and make a pull request to add it to the community.
  See :ref:`Custom pipeline modules <custom-pipeline-modules>`
  section to work with custom modules. See also
  `nf-core guidelines <https://nf-co.re/developers/guidelines>`_
  to understand how you could contribute to the community.

.. _adding-a-module-to-a-pipeline:

Adding a module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can download and add a module to your pipeline using ``nf-core/tools``::

  nf-core modules install --dir . fastqc

.. note::

  The ``--dir .`` option is optional, the default installation path is the CWD
  (that need to be your pipeline source directory)

.. hint::

  If you don't provide the module, ``nf-core`` will search
  and prompt for for a module in ``nf-core/modules`` GitHub repository

Add a simple workflow
~~~~~~~~~~~~~~~~~~~~~

In order to have a minimal pipeline, you need to add at least an unnamed workflow
to your pipeline. Moreover, you should declare the input channels and the modules
or the processes you plan to use. Suppose to create a minimal pipeline to do a *fastqc*
analysis on a set of reads. You can install the ``fastqc`` module as described
above and then add a workflow like this in your ``main.nf``:

.. code-block:: groovy

  // Declare syntax version
  nextflow.enable.dsl=2

  include { FASTQC } from './modules/nf-core/fastqc/main'

  workflow {
      reads_ch = Channel.fromFilePairs(params.input, checkIfExists: true)
          .map { it ->
              [[id: it[1][0].baseName], it[1]]
          }
          // .view()

      FASTQC(reads_ch)
  }

In this case ``FASTQC`` expect to receive a channel with *meta* information, so
this is why we create an input channel and then we add *meta* relying on file names.
Please refer to the module ``main.nf`` file to understand how to call a module
and how to pass parameters to it. Next you will need also a minimal
``nextflow.config`` configuration file to run your pipeline, in order
to define where *softwares* could be found, and other useful options:

.. code-block:: groovy

  params {
      input                       = null
  }

  profiles {
      docker {
          docker.enabled          = true
          docker.userEmulation    = true
      }
  }

  docker.registry      = 'quay.io'

Next, you can call your pipeline like this::

  nextflow run main.nf -profile docker --input "data/*_{1,2}.fastq.gz"

You can create different workflows and call them in your main workflow, or you
can install a subworkflow as like as you install a module. Also you can add
more options to your ``nextflow.config`` file, or define a custom profile
for modules, in order to provide more options to your pipeline. Please refer
to nextflow documentation to get more information on how to customize your
pipeline.

List all modules in a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can have a full list of installed modules using::

  nf-core modules list local

.. _update-a-pipeline-module:

Update a pipeline module
~~~~~~~~~~~~~~~~~~~~~~~~

You can update a module simple by calling::

  nf-core modules update fastqc

.. hint::

  Call ``nf-core modules update --help`` to get a list of the available options,
  for example, if you need to install a specific version of a module

Custom pipeline modules
-----------------------

.. _custom-pipeline-modules:

We provide custom DSL2 modules (not implemented by *nf-core* community) in our
repository at `cnr-ibba/nf-modules <https://github.com/cnr-ibba/nf-modules>`_.
This repository is not maintained by *nf-core* community, its internal and intended
to share modules across pipelines and to test stuff locally. It's organized in a
similar way to `nf-core/modules <https://github.com/nf-core/modules>`_, so it's
possible to take a module from here and share it with the *nextflow* community (please see
their `documentation <https://github.com/nf-core/modules#adding-a-new-module-file>`_).
In order to get a list of available custom modules, specify custom modules repository
using ``-g`` parameter (short option for ``--github-repository``), for example::

  nf-core modules -g https://github.com/cnr-ibba/nf-modules.git list remote

Add a custom module to a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To add a custom module to your pipeline, move into your pipeline folder and call
``nf-core install`` with your custom module repository as parameter, for example::

  nf-core modules --repository cnr-ibba/nf-modules install freebayes/single

Create a new module
~~~~~~~~~~~~~~~~~~~

You can create a new module inside a pipeline folder or inside a *modules* git cloned
folder. If you create a module inside a pipeline, you will create such module in the
``modules/local/`` folder of the pipeline, and such model will exists *only* in your
pipeline; If you create a module inside a *modules* folder, you can then install
such modules in every pipeline using ``nf-core modules install``. Creating a module
in a *modules* github folder is also the way to contribute to Nextflow community.
The command acts in the same way for both the two scenarios: relying on your project,
``nf-core modules`` will determine if your folder is a pipeline or a *modules*
repository clone::

  nf-core modules create freebayes/single --author @bunop --label process_high --meta

.. tip::

  To get more information in creating modules see `Adding a new module <https://nf-co.re/developers/adding_modules>`_
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

  NF_CORE_MODULES_TEST=1 PROFILE=docker pytest --symlink --keep-workflow-wd \
    --git-aware --tag freebayes/single

You need to check also syntax with ``nf-core`` script by specify which tests to call
using *tags*::

  nf-core modules lint freebayes/single

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
`combine operator <https://www.nextflow.io/docs/latest/operator.html#combine>`_
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
  See `DSL 2 <https://www.nextflow.io/docs/latest/dsl2.html>`_ nextflow documentation
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
on important changes with your *CVS*. For example, you could define a
custom ``params.json`` *JSON* config file in which specify your
specific requirements:

.. code-block:: json

  {
      "readPaths": "$baseDir/fastq/*.fastq.gz",
      "outdir": "results",
      "genome": "/path/to/genome.fasta"
  }

All the other parameters which cannot be specified using the command line interface
need to be provided in a *custom configuration* file using the standard nextflow
syntax:

.. code-block:: groovy

  profiles {
      slurm {
          process.executor = 'slurm'
          process.queue = 'testing'
      }
  }

Then, you can call nextflow by providing your custom parameters and configuration
file::

  nextflow run -resume main.nf -params-file params.json \
    -config custom.config -profile singularity

.. hint::

  nextflow looks for configurations in different locations, and each location is
  ranked in order to decide which settings will be applied: you can override the
  default configuration by using a configuration source with an higher priority,
  for example the ``-c <config file>``, ``-params-file <file>`` or parameters
  provided with command line are different locations where the last have the higher priority. See
  `Configuration file <https://www.nextflow.io/docs/latest/config.html#configuration-file>`_
  section of nextflow documentation.

Add test data to your pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It frustrating writing a pipeline on a real dataset: steps could require a lot
of time to be completed and if you made any errors when calling software or when
collecting outputs you will be noticed after a long period of time and you have
no way to recover the data you have with a nextflow error.
In *testing* and *revision* stages or when adding new features, consider
to work with a *reference data sets* like the
one provided by `nextflow community <https://github.com/nf-core/test-datasets>`_
or add some public data to your pipeline. Please, remember to not track big files
with your CVS: you should provide the minimal requirements to get your pipeline
running as intended in the shortest time. You should also consider
to provide a ``test`` profile with the required parameters which let you to test
your pipeline like this::

  nextflow run . -profile test,singularity

Where the ``test`` profile is specified in ``nextflow.config`` and refers to
the *test dataset* you provide with your pipeline:

.. code-block:: groovy

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
`GitHub workflow <https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions>`_.

Lower resources usage
~~~~~~~~~~~~~~~~~~~~~

You should consider to lower the resources required by your pipeline. This will
avoid the costs of allocating more resources than needed and will let you complete
your analysis in a shorter time when resources are limited.
Take a look at :ref:`dynamic-allocation-resources` documentation section.
