
Customize a Pipeline
====================

.. contents:: Table of Contents

Cloning a pipeline
------------------

The easiest way to modifying an existing pipeline is to clone it from the github
repository::

  git clone https://github.com/nf-core/rnaseq

.. hint::

  nextflow itself can clone a pipeline like git does::

    nextflow clone nf-core/rnaseq

However, you don't need to modify a pipeline if you need only to change a pipeline
parameter or adapt the execution to your local environment: a pipeline execution
is high customizable by providing *custom configuration files* and *parameters*. Cloning
a pipeline is useful when you need to add new features or to fix bugs in a pipeline
you are working on.

.. warning::

  if you clone a pipeline with ``nextflow clone`` command, ensure that git *remotes* are
  correct and point to the repository location

.. _configuring-a-pipeline:

Configuring a pipeline
----------------------

You can customize a pipeline by creating custom configuration files: this could
be necessary if you need to lower the requirements of a pipeline, for example,
in order to run a pipeline with limited resources or if you need to track the
parameters you are been using for a particular analysis. You can also specify a custom
configuration file in order to run a pipeline with a different profile, for example
to enable different options required to a specific environment. A custom configuration
file has an higher priority than the default configuration file, but will have a lower
priority than the parameters provided with command line. Moreover, is it possible
to use an *institutional configuration file* in which you can specify the default
parameters for all the pipelines you plan to execute within the infrastructure
provided by your institution (see `here <https://github.com/nf-core/configs>`_
for an example). For a complete list of
configuration options and priorities, please see the
`nextflow configuration <https://www.nextflow.io/docs/latest/config.html>`_ documentation.

nextflow.config
~~~~~~~~~~~~~~~

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

.. _institutional-configuration-files:

Institutional configuration files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nextflow offers a GitHub repository where institutional configuration files can
be stored and shared among users. This means that users belonging to the same
institution can share configuration files that are specific to their infrastructure.
This repository is located at `<https://github.com/nf-core/configs>`_ and is
structured in mainly two sections, configuration that are shared among all pipelines
and configuration that are specific to a single pipeline. Usually the first configuration
files keeps information about *executors*, *queues*, *resources* and
they can be applied to all pipelines independently in a particular computing
environment in your institute. The second
configuration files are specific to a single pipeline and can be used to customize
a single pipeline step, for example to change the number of CPUs or the amount of memory
required by a single process overriding the pipeline default configuration.

Institutional configuration files are managed through the
`profile scope <https://www.nextflow.io/docs/latest/config.html#config-profiles>`_
and usually the *nf-core* community pipelines are already configured to use them.
This means that if an institutional configuration file is available in the nf-core
configs repository, it can be using passing the profile name to the pipeline execution,
for example:

.. code-block:: bash

  nextflow run nf-core/rnaseq --profile <my_institution> ...

This is enough to apply the global institutional configuration to the pipeline execution
and the pipeline specific configuration if available. For more information
see the
`Shared nf-core/configs <https://nf-co.re/docs/usage/getting_started/configuration#shared-nf-coreconfigs>`_
and the `Step-by-step guide to writing an institutional profile <https://nf-co.re/docs/tutorials/use_nf-core_pipelines/writing_institutional_profiles>`_
documents for more information.

.. tip::

  We have a custom *institutional* configuration repository at *ibba*. To use it
  with nf-core pipelines, you should add the repository
  `cnr-ibba/nf-configs <https://github.com/cnr-ibba/nf-configs/>`_ using, the
  ``--custom_config_base`` option, and specify `ibba` and your working environment
  profile, for example:

  .. code-block:: bash

    nextflow run nf-core/rnaseq \
      --custom_config_base https://raw.githubusercontent.com/cnr-ibba/nf-configs/ibba \
      --profile ibba,core \
      ...

  cnr-ibba pipelines, like `cnr-ibba/nf-resequencing-mem <https://github.com/cnr-ibba/nf-resequencing-mem>`_
  are already configured to use our local institutional configuration repository.
  See `nf-core/configs: IBBA Configuration <https://github.com/cnr-ibba/nf-configs/blob/ibba/docs/ibba.md>`_
  for more information.

.. hint::

  The institutional configuration files are accessed remotely during pipeline execution:
  if you need to work offline, you should download and manage a local copy and provide
  the path to the institutional configuration file using the ``-config`` option and
  the institutional configuration git repository though the ``--custom_config_base``
  option. More information can be found in :ref:`running-nextflow-offline`
  and :ref:`cloning-institutional-configuration-files` of this documentation.

Custom configuration files
~~~~~~~~~~~~~~~~~~~~~~~~~~

There are other configuration files that can be used to customize a single pipeline
and can be stored in the pipeline directory or in the directory where you are running
the pipeline. Those configuration files have the highest priority and can be used
to customize a single pipeline execution for a particular project. Those configuration
files should be specified using the ``-c`` or ``-config`` option when running the pipeline,
for example:

.. code-block:: bash

  nextflow run nf-core/rnaseq -c custom.config ...

.. warning::

  Avoid to name your custom config file as ``nextflow.config``, since is a reserved
  name for the default configuration file, which is loaded automatically by nextflow
  if present in the pipeline directory. If you name your custom configuration file
  with a different name, you can control when it's loaded using the ``-c`` or
  ``-config`` option when running nextflow.

More information about configuration customization can be found in the official
nextflow `Configuration <https://www.nextflow.io/docs/latest/config.html>`_.
The reference of all configuration options could be found at nextflow
`Configuration options <https://www.nextflow.io/docs/latest/reference/config.html>`_
reference. Here we provide some examples of how to customize a pipeline using
custom configuration files.

Process selectors
^^^^^^^^^^^^^^^^^

Nextflow let you to specify the behavior of a process or a group of processes
using process `selectors <https://www.nextflow.io/docs/latest/config.html#process-selectors>`_
in the configuration files. There are mainly two types of selectors:
``withLabel`` and ``withName``: the first one let you to specify the requirements
for every process having the same label, the second one let you to specify the
requirements for a process by name. More precisely, in DSL2 pipelines, this requirements
are specified in ``conf/base.config`` and ``conf/modules.config`` where the first
file is used to specify the requirements for a group of jobs using *labels* and
the second one is used to specify the requirements for a single process.

The Nextflow community recommend to use labels to specify the requirements for
a group of processes when possible using ``withLabel``: when there's
the need to specify the requirements for a single process, you can use the ``withName``
selector. For example, to lower resources requirements, it's better to
start by redefining the most used labels, like ``process_high`` and ``process_medium``,
and after redefine single processes. Start with an empty *custom configuration*
file and add a ``process`` scope like this:

.. code-block:: groovy

  process {
      withLabel: process_low {
          ...
      }
      withLabel: process_medium {
          ...
      }
      withLabel: process_high {
          ...
      }
      withName: FASTQC {
          ...
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
  the minimal parameters required by your pipeline. See nextflow documentation for
  `Process selectors <https://www.nextflow.io/docs/latest/config.html#process-selectors>`_
  for more information.

.. _dynamic-allocation-resources:

Dynamic allocation of resources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is possible that different instances of a process require different resources
in terms of computing power, memory, or time. In such situations, requesting, for example,
an amount of memory too low will cause some tasks to fail. Instead, using a
higher limit that fits all the tasks in your execution could significantly
decrease the execution priority of your jobs. In such cases, the
`Dynamic directives <https://www.nextflow.io/docs/latest/process.html#dynamic-directives>`_
could be useful to increase the resources required by a process if the task fails
and is retried. For example, Nextflow let you to specify the resources
required by a process dynamically using the ``task.attempt`` variable. This variable
is a counter that is incremented each time a task is retried. For example, you can
specify the resources required by a process like this:

.. code-block:: groovy

  process {
      withLabel:process_medium {
          cpus   = { 6     * task.attempt }
          memory = { 12.GB * task.attempt }
          time   = { 8.h   * task.attempt }
      }
  }

Other directives that affect the dynamic allocation of resources when a task is retried
are `errorStrategy <https://www.nextflow.io/docs/latest/reference/process.html#errorstrategy>`_
and `maxRetries <https://www.nextflow.io/docs/latest/reference/process.html#maxretries>`_:
the first one let you to specify the behavior of a process when an error occurs,
and you can configure this option to terminate the pipeline when an error is found or
continue with the workflow just ignoring the error. The second one let you to specify the
maximum number of retries for a process, after that value is reached, the entire
pipeline will be terminated. Usually, these directive are defined by default in
``conf/base.config`` file of the pipeline like this:

.. code-block:: groovy

  process {
      errorStrategy = { task.exitStatus in ((130..145) + 104) ? 'retry' : 'finish' }
      maxRetries    = 1
  }

But eventually, you can override these directives for a particular process using
the ``withName`` or ``withLabel`` process selectors in the custom configuration file.
You can use more complex *closures* to define the behavior of a process when an error
occurs. For example, you can specify that a process should be retried if it fails
until a maximum number of retries is reached. After that, we just ignore the error
and continue with the workflow:

.. code-block:: groovy

  process {
      errorStrategy  { task.attempt <= maxRetries  ? 'retry' : 'ignore' }
  }

.. hint::

  This can be possible if there are no dependent processes that require the output
  of the process that failed. Take a look to the
  `Handling failing jobs with Nextflow <https://lucacozzuto.medium.com/handling-failing-jobs-with-nextflow-24405b97b679>`_
  medium article to get more hints on how to handle failing jobs in Nextflow.

Setting max amount of resources for a process
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Nextflow will also let you to specify the maximum resources required by a process
using the `resourceLimits <https://www.nextflow.io/docs/latest/reference/process.html#resourcelimits>`_
directive: this could be specified at the task level or globally at the process level.
In the latter case, you will set the maximum resources required by every process
called by the pipeline. An example of how to specify the maximum resources required
by a process is shown below:

.. code-block:: groovy

  process {
      resourceLimits = [
          cpus: 32,
          memory: 64.GB
      ]
  }

.. warning::

  When using the `resourceLimits` directive, you are only declare the maximum
  amount of resources that a process can require, you are not specifying the
  total amount of resources that will be used by all the process during the
  pipeline execution.

.. hint::

  The `resourceLimits` directive was introduced in Nextflow version ``24.04.0``:
  the pipeline options ``--max_cpus``, ``--max_memory`` and ``--max_time`` are
  deprecated and will be removed in future versions. If you need to work
  with pipelines developed with older versions of Nextflow, you should use the
  old ``check_max`` function to ensure that resource requirements don't exceed
  a maximum limit. See the `Dynamic allocation of resources (old syntax)`_
  section for more information.

.. tip::

  If you need to know if your pipeline support the newest ``resourceLimits`` directive,
  take a look at ``nextflow.config`` file in the pipeline directory and in the
  ``conf/base.config`` file: if the dynamic allocation of resources is managed by
  the ``check_max`` function and by the ``max_cpus``, ``max_memory`` and ``max_time``
  parameters, you should use the old syntax to manage resources.


Dynamic allocation of resources (old syntax)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Before version ``24.04.0``, Nextflow let you specify the maximum resources required
by a process using the ``--max_cpus``, ``--max_memory`` and ``--max_time`` parameters.
The resources were allocated dynamically using the ``check_max`` function, which
needs to be included in the custom configuration file or in any files that make
use of the ``check_max`` function to dynamically allocate resources.
You should remember to specify a default value for ``max_memory``, ``max_cpus``,
and ``max_time`` in your *custom configuration file* to avoid warnings
when the ``check_max`` function is evaluated. An example of how to specify the maximum
resources required by a process with the old syntax is shown below:

.. code-block:: groovy

  params {
      // Max resource options
      // Defaults only, expecting to be overwritten
      // need to be specified in order to ``check_max`` function to work
      max_memory                 = '64.GB'
      max_cpus                   = 32
      max_time                   = '240.h'
  }

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
  submitted using the nextflow *params file* or *command line interface*.

Remove process limits
^^^^^^^^^^^^^^^^^^^^^

Sometimes could be convenient to remove the limits set by a process, for example
a very long task that requires a lot of time to be completed: in this case, will
be more convenient to avoid setting a walltime limit and let the *executor* choose
the max allowed value. You can simply unset the time limit for a process by setting
a ``null`` value for the time parameter in the custom configuration file, for example:

.. code-block:: groovy

  process {
      withLabel:unlimited_time {
          time   = null
      }
  }

This will override all the time limits set by the process and will let the *executor*
to choose the max allowed value (if supported).

Provide custom parameters to a process
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Some modules may require additional parameters to be provided in order to work
correctly. This parameters can be specified with the ``ext.args`` variable within
the process scope in the custom configuration file, for example:

.. code-block:: groovy

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

Change output file names
^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes could be useful to change the output file names of a process, for example
when applying a process which keeps the same input file name in input and output.
Ideally, the output file name *prefix* is defined at process level like this:

.. code-block:: groovy

  script:
  def args = task.ext.args ?: ''
  def prefix = task.ext.prefix ?: "${meta.id}"

So it is possible to configure a ``task.ext.prefix`` variable in the custom configuration
file to define the output file name prefix, for example:

.. code-block:: groovy

  process {
      withName: SEQKIT_RMDUP_R1 {
          ext.prefix = { "${meta.id}_R1" }
      }
  }

In this example we use *closures* to define the output file name prefix *dynamically*,
an this is useful to keep *sample name* in output file. In alternative, is possible
to modify the `meta.id` using the
`map operator <https://www.nextflow.io/docs/latest/reference/operator.html#operator-map>`_,
for example:

.. code-block:: groovy

  channel.map { meta, it -> [[id: "${meta.id}_updated"], it] }

However, this will override the old ``meta.id`` value with the new one, and all
the processes will then use the new value to define their output file name prefix.
A third option could be to use the
`publishDir <https://www.nextflow.io/docs/latest/reference/process.html#publishdir>`_
directive and define a closure to define the output file name prefix, for example:

.. code-block:: groovy

  publishDir 'results', saveAs: { filename -> "foo_$filename" }

See `Store outputs renaming files <https://nextflow-io.github.io/patterns/publish-rename-outputs/>`_
on `nextflow patterns <https://nextflow-io.github.io/patterns/>` for more information.

Create a custom profile
^^^^^^^^^^^^^^^^^^^^^^^

A profile is a set of parameters that can be used to run a pipeline in a specific
environment. For example, you can define a profile to run a pipeline in a cluster
environment, or to run a pipeline using a specific container engine. You can also
define a profile to run a pipeline with a specific set of parameters, for example
test data.
A profile is defined in a configuration file, which is specified
using the ``-profile`` option when running nextflow. A profile require a name
which is used to identify the profile and a set of parameters. For example, you
can define a profile like this in your ``custom.config`` file:

.. code-block:: groovy

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

  nextflow run -profile cineca,singularity ...

Params file
~~~~~~~~~~~


A Nextflow JSON parameter file is a way of providing configuration parameters for
a Nextflow pipeline in a structured format using JSON (JavaScript Object Notation).
It allows users to define various parameters required by the pipeline in a file
rather than passing them directly via the command line.
The main key features of a Nextflow JSON parameter File are

1. **Structure**: The JSON file contains key-value pairs that define different
   parameters. This structure makes it easy to read and modify parameters without
   needing to remember command line syntax.
2. **Use Case**: JSON parameter files are particularly useful for complex workflows
   with many parameters or when those parameters are subject to frequent changes.
   Users can manage their configurations in one place.
3. **Access in Pipeline**: Parameters defined in the JSON file can be accessed
   directly in your Nextflow scripts using the `params` object.

Here’s a simple example of what a Nextflow JSON parameter file might look like:

.. code-block:: json

  {
    "input": "data/input_file.txt",
    "output": "results/",
    "other_param": "value"
  }


To use a JSON parameter file in a Nextflow pipeline, you can specify it on the
command line using the `-params-file` option:

.. code-block:: bash

  nextflow run <your pipeline> -params-file params.json

The benefits of using a JSON parameter file include:

- **Readability**: JSON files are quite structured and make it easy to see the
  settings needed for a pipeline.
- **Convenience**: It’s more convenient to edit a JSON file for changing
  parameters than to modify and remember long command-line options.
- **Version Control**: JSON files can be easily tracked and managed using
  version control systems like Git, which is particularly useful for
  collaborative projects.
- **Compatibility**: JSON is widely supported across different programming
  languages, making it easy to generate or manipulate if needed.
