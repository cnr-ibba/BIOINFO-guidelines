
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

Configuring a pipeline
----------------------

You can customize a pipeline by creating a custom configuration file. This could
be necessary if you need to lower the requirements of a pipeline, for example,
in order to run a pipeline with limited resources or to avoid to provide pipeline
parameters using the command line interface. You can also specify a custom
configuration file in order to run a pipeline with a different profile, for example
to enable different options required to a specific environment. A custom configuration
file has an higher priority than the default configuration file, but will have a lower
priority than the parameters provided with command line. For a complete list of
configuration options and priorities, please see the
`nextflow config <https://www.nextflow.io/docs/latest/config.html>`__ documentation.
Before starting with a new custom configuration file, you should take a look to
the default configuration file provided by the pipeline you are working on. For
a standard nextflow pipeline, the default configuration file is named ``nextflow.config``
and is located on the root of the pipeline directory. In this file there are defined
the default parameters that affect pipeline execution. In a DSL2 pipeline, you can
also find the ``conf/base.config`` file, in which the requirements for each job
are defined.

.. hint::

  Is recommended by the community that the pipeline parameters, like the input files,
  the reference database used or user defined values need to be provided by a *parameters*
  file, which is defined as a JSON file and is specified with the ``-params-file``
  option. This let you to run a pipeline without
  providing parameters using the command line interface. All the parameters which
  cannot be specified using the command line interface (for example the amount of
  memory required by a certain step) can be defined in the custom configuration file.

.. warning::

  Avoid to name your custom config file as ``nextflow.config``, since is a reserved
  name for the default configuration file, which is loaded automatically by nextflow
  if present in the pipeline directory. If you name your custom configuration file
  with a different name, you can control when it's loaded using the ``-c`` or
  ``-config`` option when running nextflow.

Lowering pipeline requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nextflow let you to specify the amount of resources required by a pipeline step
using process `selectors <https://www.nextflow.io/docs/latest/config.html#process-selectors>`__
in the configuration files. More precisely, in DSL2 pipelines, this requirements
are specified in ``conf/base.config`` file. There are mainly two types of selectors:
``withName`` and ``withLabel``: the first one let you to specify the requirements
for a process by name, the second one let you to specify the requirements for every
process having the same label. To lower resources requirements, it's better to
start by redefining the most used labels, like ``process_high`` and ``process_medium``,
and then redefine single process by names. Start with an empty configuration
file and add a ``process`` scope like this::

  process {
      withLabel:process_single {
          memory = 1.G
      }
      withLabel:process_low {
          memory = 4.G
      }
      withLabel:process_medium {
          memory = 12.G
      }
      withLabel:process_high {
          memory = 48.G
      }
  }

You may want to explore the imported modules tho understand will processes will
be affected by which label.
In order to get effect, you need to provide this file with the nextflow ``-c``
or ``-config`` option:

.. code-block:: bash

  nextflow run -c custom.config ...

.. hint::

  Since these parameters will override the default ones, it's better to declare only
  the minimal parameters required by your pipeline.

You can also declare resources dynamically. For example, you can make use of the
``check_max`` function, but you will require to define the ``check_max`` function
in your custom configuration file::

  process {
      withLabel:process_medium {
          cpus   = { check_max( 6     * task.attempt, 'cpus'    ) }
          memory = { check_max( 12.GB * task.attempt, 'memory'  ) }
          time   = { check_max( 8.h   * task.attempt, 'time'    ) }
      }
  }

  // Function to ensure that resource requirements don't go beyond
  // a maximum limit
  def check_max(obj, type) {
      if (type == 'memory') {
          try {
              if (obj.compareTo(params.max_memory as nextflow.util.MemoryUnit) == 1)
                  return params.max_memory as nextflow.util.MemoryUnit
              else
                  return obj
          } catch (all) {
              println "   ### ERROR ###   Max memory '${params.max_memory}' is not valid! Using default value: $obj"
              return obj
          }
      } else if (type == 'time') {
          try {
              if (obj.compareTo(params.max_time as nextflow.util.Duration) == 1)
                  return params.max_time as nextflow.util.Duration
              else
                  return obj
          } catch (all) {
              println "   ### ERROR ###   Max time '${params.max_time}' is not valid! Using default value: $obj"
              return obj
          }
      } else if (type == 'cpus') {
          try {
              return Math.min( obj, params.max_cpus as int )
          } catch (all) {
              println "   ### ERROR ###   Max cpus '${params.max_cpus}' is not valid! Using default value: $obj"
              return obj
          }
      }
  }

The ``--max_cpus``, ``--max_memory`` and ``--max_time`` parameters are the maximum
allowed values for dynamic job requirements: by setting these parameters you can
ensure that a *single job* will not allocate more resources than the ones you have
declared. Those parameters have not effect on the *global* resources used or the
number of job submitted.

.. hint::

  ``--max_cpus``, ``--max_memory`` and ``--max_time`` are parameters that can be
  submitted using the nextflow *params file* or command line interface.

Provide custom parameters to a process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some modules may require additional parameters to be provided in order to work
correctly. This parameters can be specified with the ``ext.args`` variable within
the process scope in the custom configuration file, for example::

  process {
      withName:process_fastqc {
          ext.args = '-t 4'
      }
  }

When a process is composed by two (or more) tools, you can specify parameters for
each process independently, using ``ext.args``, ``ext.args2``, ``ext.args3``:
``ext.args`` will be used for the first process, ``ext.args2`` for the second and
so on. In a DSL2 pipeline, custom variables for each process are defined in
``conf/base.config`` file: take a look to this file to understand which variables
are set by default in your pipeline and before adding new variables to a process.

Create a custom profile
~~~~~~~~~~~~~~~~~~~~~~~

A profile is a set of parameters that can be used to run a pipeline in a specific
environment. For example, you can define a profile to run a pipeline in a cluster
environment, or to run a pipeline using a specific container engine. You can also
define a profile to run a pipeline with a specific set of parameters, for example
test data.
A profile is defined in a configuration file, which is specified
using the ``-profile`` option when running nextflow. A profile require a name
which is used to identify the profile and a set of parameters. For example, you
can define a profile like this in your ``custom.config`` file::

  profiles {
      cineca {
          process {
              clusterOptions = { "--partition=g100_usr_prod --qos=normal" }
          }
      }
  }

In this example, each process will be submitted to the ``g100_usr_prod`` partition
using the ``normal`` quality of service, and those parameters may depend on the
environment in which this pipeline is supposed to run. In another environment,
those parameter will not apply, so there's no need to use this specific profile
in a different environment. You can the call your pipeline using the ``-profile``
option::

  $ nextflow run -profile cineca,singularity ...

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
  $ touch main.nf nextflow.config modules.json README.md

Next you have to edit modules.json in order to have minimal information::

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

You can create a new module inside a pipeline folder or inside a *modules* git cloned
folder. If you create a module inside a pipeline, you will create such module in the
``modules/local/`` folder of the pipeline, and such model will exists *only* in your
pipeline; If you create a module inside a *modules* folder, you can then install
such modules in every pipeline using ``nf-core modules install``. Creating a module
in a *modules* github folder is also the way to contribute to Nextflow community.
The command acts in the same way for both the two scenarios: relying on your project,
``nf-core modules`` will determine if your folder is a pipeline or a *modules*
repository clone::

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
on important changes with your *CVS*. For example, you could define a
custom ``params.json`` *JSON* config file in which specify your
specific requirements::

  {
      "readPaths": "$baseDir/fastq/*.fastq.gz",
      "outdir": "results",
      "genome": "/path/to/genome.fasta"
  }

All the other parameters which cannot be specified using the command line interface
need to be provided in a *custom configuration* file using the standard nextflow
syntax::

  profiles {
      slurm {
          process.executor = 'slurm'
          process.queue = 'testing'
      }
  }

Then, you can call nextflow by providing your custom parameters and configuration
file::

  $ nextflow run -resume main.nf -params-file params.json \
    -config custom.config -profile singularity

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

Lower resources usage
~~~~~~~~~~~~~~~~~~~~~

You should consider to lower the resources required by your pipeline. This will
avoid the costs of allocating more resources than needed and will let you complete
your analysis in a shorter time when resources are limited.
Take a look at `Lowering pipeline requirements`_ documentation section.
