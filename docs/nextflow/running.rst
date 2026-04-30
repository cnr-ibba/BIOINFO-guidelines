
Running Nextflow
================

.. contents:: Table of Contents

A note on containers
--------------------

Despite nextflow could be run using :doc:`conda <../general/conda>`,
:doc:`singularity <../general/singularity>`, :doc:`docker <../general/docker>`
or other container runtimes, the recommended container application to use
is **singularity**: this solution in fact manages all software dependencies
in a unique file and could be cached and reused in order to speed up the
calculation process (see :ref:`set-nxf-singularity-cache` for more information).
You can have more information about singularity in
the :ref:`singularity <about-singularity>` section of this guidelines.

You can select the type of container runtime to use with the
``-profile`` option, for example:

.. code-block:: bash

  nextflow run nf-core/rnaseq -profile test,singularity -resume

.. warning::

  Downloading software dependencies could take a lot of time and could be subject
  to networking errors, which are not related to pipelines or data but can slow or
  broke pipeline execution. In such way, it's better to configure **caches** when
  downloading softwares: singularity cache could be configured in
  `singularity scope <https://www.nextflow.io/docs/edge/reference/config.html#singularity>`_
  or better using ``$NXF_SINGULARITY_CACHEDIR``.
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR <set-nxf-singularity-cache>` for more information

Nextflow parameters and pipeline parameters
-------------------------------------------

There are two types of parameters you can pass to nextflow: *nextflow parameters*
and *pipeline parameters*. Nextflow parameters are related to nextflow itself,
like ``-resume`` or ``-log``. Pipeline parameters are related to the pipeline
you are running, like ``--input`` or ``--output``. In general, nextflow parameters
have only one ``-`` before the parameter name, while pipeline parameters have two
``--``. To get a full list of available options, you can call nextflow with ``-h``
parameter or without any parameter::

  $ nextflow -h

While to have a list of parameters for a specific pipeline,
you can call the pipeline with ``--help`` option, for example::

  $ nextflow run nf-core/rnaseq --help

Another important aspect if that pipeline parameters can be written in a json file
and provided to nextflow with the ``-params-file`` option. This is useful when you
have a lot of parameters to provide to the pipeline, or when you want to save a
configuration for later use. For example, to provide a json file with parameters
to the pipeline, you can do::

  $ nextflow run nf-core/rnaseq -params-file params.json

where ``params.json`` is a json file with the following content::

  {
    "input": "samplesheet.csv",
    "fasta": "path/to/genome.fasta"
  }

Nextflow parameters and pipeline parameters are not the only way to customize a
pipeline: nextflow allows to define custom configuration files in which you can
customize other aspects of the pipeline, like the number of CPUs to use, the
memory to allocate, environment variables and also settings specific to the
running environment in which the pipeline is called. For more information, see the
`Configuration file <https://www.nextflow.io/docs/latest/config.html#configuration-file>`_
section of the nextflow documentation.
See also :ref:`Configuring a pipeline <configuring-a-pipeline>` section of this
guidelines for more information. To get more information on CLI and pipeline
options, please see
`Command line <https://www.nextflow.io/docs/latest/cli.html#command-line>`_, and
both `CLI reference <https://www.nextflow.io/docs/latest/reference/cli.html>`_
and `Pipeline parameters <https://www.nextflow.io/docs/latest/cli.html#pipeline-parameters>`_
from nextflow documentation.

Execute a community pipeline
----------------------------

Nextflow lets to build and share bioinformatics pipelines across the community. The
simples way to use nextflow is to identify the pipeline you need, check for its requirements
and then launch it using your data. Since all the nextflow community pipelines
are public, you could download and modify them according your needs.

Search for a community pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Community pipelines are available at `nf-core pipelines <https://nf-co.re/pipelines>`_
site: you could search a pipeline and browse its documentation in the
`nf-core website <https://nf-co.re/>`_.
For example, by searching for ``rnaseq`` you could reach the
`rnaseq pipeline <https://nf-co.re/rnaseq>`_
page project and get documentation on its usage by clicking on
`Usage <https://nf-co.re/rnaseq/usage>`_ tab.

You can download a pipeline using ``nextflow pull`` followed by the pipeline
like ``<organization name>/<pipeline>``, for example:

.. code-block:: bash

  nextflow pull nf-core/rnaseq

This will download a copy of the pipeline in a nextflow *cache* folder, which
usually is ``$HOME/.nextflow/assets``: the pipeline will be placed in a subfolder
for the organization and pipeline name (in this case ``nf-core/rnaseq``). The
containers files required to execute the pipeline will be downloaded when the
pipeline is executed for the first time: please check for internet connection
during pipeline execution: if it not possible to download the container, there's
the possibility to run nextflow offline. Please see
:ref:`Running nextflow offline <running-nextflow-offline>` of this documentation
and the official `Running offline <https://nf-co.re/docs/usage/getting_started/offline>`_
nextflow documentation for more information.

.. hint::

  The organization name is the GitHub organization which hosts the pipeline, like
  `nf-core GitHub <https://github.com/nf-core>`_ or `cnr-ibba <https://github.com/cnr-ibba>`_,
  while the pipeline name is the name of the GitHub repository which contains the
  pipeline. You could derive the pipeline name by removing ``https://github.com/``
  from the repository URL. For example, from
  `<https://github.com/nf-core/rnaseq>`_ you can derive the pipeline
  named ``nf-core/rnaseq``.

.. tip::

  You can get a list of available `nf-core pipelines <https://nf-co.re/pipelines>`_
  using `nf-core/tools <https://github.com/nf-core/tools>`_ with
  ``nf-core pipelines list`` command. You can also add a pattern to search for
  a specific pipeline, for example::

    nf-core pipelines list rna

  to get a list of pipelines related to RNA analysis.

In order to download the pipeline, the softwares, and testing all in your local
environment (which is recommended to see that all the stuff works as intended,
see :ref:`run-a-pipeline-with-test-data`) you can call directly the nextflow
pipeline on *test data*, for example for the *rnaseq* pipeline:

.. code-block:: bash

  mkdir nf-rnaseq
  cd nf-rnaseq
  nextflow run nf-core/rnaseq -profile test,singularity -resume

.. hint::

  Calling ``nextflow run`` with a remote pipeline will place the ``work`` and
  ``results`` directories in the current working directory, with some other hidden
  files useful for logging the pipeline execution in the current directory.
  For such reason, it's better to create an empty project directory in which
  calling ``nextflow run`` or create a new directory for the project in which
  you plan to run the pipeline.

.. tip::

  The community pipelines have a ``--help`` option to show all supported parameters.
  try:

  .. code-block:: bash

    nextflow run nf-core/rnaseq --help

  To get a full list of the available options

.. warning::

  It is possible that the nextflow version required by the pipeline is different
  from your nextflow version installed and you couldn't execute the pipeline. Please
  see :ref:`this section <nextflow-version-required>` of nextflow troubleshooting.

When calling nextflow using a community pipeline like ``nextflow run nf-core/rnaseq``,
nextflow will download the latest pipeline version, and will place a local copy of
the pipeline in your ``$HOME/.nextflow/assets`` folder. This local copy of
the pipeline is called whenever you will call ``nextflow run`` using the same pipeline.
If you need a particular version or branch of such pipeline, you can indicate such
requirement with the ``-r`` option, for example::

  $ nextflow pull nf-core/rnaseq -r 3.12

.. warning::

  Whenever you pull a pipeline version different from the latest, you **MUST** declare
  the same version or branch when calling nextflow, for example::

    $ nextflow run nf-core/rnaseq -r 3.12 --help

  If you need to update your local pipeline to latest version see the
  :ref:`Update a pipeline <update-a-pipelines>` section.

.. _manage-community-pipelines:

Manage community pipelines with ``nf-core``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Search for a pipeline
^^^^^^^^^^^^^^^^^^^^^

Whenever you run a community pipeline, nextflow will download and cache it (in
your ``$HOME/.nextflow/assets/`` folder). You could check your installed community pipelines
with::

  nextflow list

You can list all the available ``nf-core`` pipelines with::

  nf-core pipelines list

You could search for a specific pipeline by providing a name as an argument::

  nf-core pipelines list rna

Download a pipeline
^^^^^^^^^^^^^^^^^^^

.. _nf-core-pipelines-download:

You can download a pipeline with its container dependencies. This will be helpful
when running nextflow in an environment without internet connection::

  nf-core pipelines download nf-core/rnaseq -r 3.12.0

this command let the possibility to amend singularity images in your
``$NXF_SINGULARITY_CACHEDIR``, which means that images will not be placed in the
archive but in your local ``$NXF_SINGULARITY_CACHEDIR`` folder if missing.

.. hint::

  using the option ``--download-configuration yes`` you can download also the
  institutional configuration file for *offline* usage. This is useful when you
  need to run a pipeline in an environment without internet connection. For more
  information see :ref:`institutional-configuration-files` and :ref:`running-nextflow-offline`.

Run a pipeline
^^^^^^^^^^^^^^

The most interesting thing is the possibility to configure params interactively with::

  $ nf-core pipelines launch rnaseq

This command will download the pipeline in the ``assets`` folder and then will
open a web browser or a CLI interactive session to let you configure the pipeline
parameters interactively. You can also save the configuration in a file and use it later
with the nextflow ``-params-file`` option.

See :ref:`Install nf-core/tools <install-nf-core>` to get ``nf-core/tools`` software
installed

.. tip::

  nextflow creates a lot of file in the current working directory. It's better to
  create a custom directory in which nextflow can be called

Execute a shared custom pipeline
--------------------------------

Nextflow is able to manage pipelines outside the scope of the **nf-core** team, if
they are shared in public repositories. For example, to execute a pipeline available
on GitHub, call nextflow with ``<profile/project>`` like the following example::

  nextflow run cnr-ibba/nf-resequencing-mem -resume -profile singularity \
    --input <samplesheet.csv> --genome_fasta <path/to/genome.fasta>

where `cnr-ibba/nf-resequencing-mem <https://github.com/cnr-ibba/nf-resequencing-mem>`_
is the repository which contains the nextflow pipeline.

.. tip::

  You can configure nextflow to store your GitHub access credentials, see
  :ref:`Access to private repositories <nextflow-private-repo>` of this guidelines

Nextflow best-practices
-----------------------

Here are some tips that could be useful while running nextflow.

.. _run-a-pipeline-with-test-data:

Run a pipeline with test data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you run a pipeline for the first time, it's better to use test data in order
to check if the pipeline is working as expected. All the community pipelines have
a ``-profile test`` option which will download a small dataset and run the pipeline
on it. For example, to run the ``nf-core/rnaseq`` pipeline with test data, you can
do::

  nextflow run nf-core/rnaseq -profile test,singularity -resume

This will also download the required dependencies (like the singularity images).
Next time you will run the pipeline, nextflow will use the cached images and will
not download them again.

Getting information from logs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By calling ``nextflow log`` you can get information on your last nextflow runs,
which includes timestamp, duration, status, *run name* and the command used when
the pipeline was called::

  $ nextflow log
  TIMESTAMP               DURATION        RUN NAME                STATUS  REVISION ID     SESSION ID                              COMMAND
  2021-10-27 12:40:32     54.8s           serene_engelbart        OK      c44b10f3aa      598f0939-a7b0-497f-a16f-b2431a7e5ee3    nextflow run . -profile test,docker
  2021-10-27 12:49:05     43.6s           evil_ride               OK      c44b10f3aa      a70a75e2-61fc-4407-aba4-19ac33f31774    nextflow run . -profile test,docker

``RUN NAME`` is an arbitrary name assigned to your pipeline. By calling ``nextflow log``
again and providing such name you can retrieve more information on single execution
steps::

  $ nextflow log serene_engelbart
  /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/5d/6ff357b9b679198557bf22d24adf1e
  /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/ff/dd919f582e8583a16aecc58f6cc093
  /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/74/944e234214bcca20209637a94c0ac2
  /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/31/b075adb744673b9cc8fb214729c455

By defaults ``nextflow log <run name>`` will return only the working directory, to
get more informative results you need to specify some columns using ``-f`` parameter,
for example::

  $ nextflow log serene_engelbart -f 'process,status,exit,hash,duration,workdir'
  NFCORE_RESEQUENCING:RESEQUENCING:INPUT_CHECK:SAMPLESHEET_CHECK  COMPLETED       0       5d/6ff357       1.8s    /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/5d/6ff357b9b679198557bf22d24adf1e
  NFCORE_RESEQUENCING:RESEQUENCING:FASTQC COMPLETED       0       ff/dd919f       7.2s    /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/ff/dd919f582e8583a16aecc58f6cc093
  NFCORE_RESEQUENCING:RESEQUENCING:FASTQC COMPLETED       0       74/944e23       5.2s    /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/74/944e234214bcca20209637a94c0ac2
  NFCORE_RESEQUENCING:RESEQUENCING:FASTQC COMPLETED       0       31/b075ad       7.2s    /home/paolo/Projects/NEXTFLOWetude/nf-core-resequencing/work/31/b075adb744673b9cc8fb214729c455

Call ``nextflow log -l`` to have a full list available columns.

Resume calculations
~~~~~~~~~~~~~~~~~~~

Nextflow, by default, executes every calculation in a subfolder inside the
``work`` directory in your current working directory. Every steps is executed in
separate subfolders and nextflow will take care about *inputs* and *outputs* among
related steps. It is frequent to call nextflow multiple times, for example while
modifying a pipeline or while tuning parameters or solving issues.
In such way, you can save a lot of spaces (and calculation times)
by *resuming* a pipeline (aka. don't run job completed with success). To achieve this,
is important to add the ``-resume`` option while calling nextflow::

  $ nextflow run <pipeline> -resume <pipeline parameters>

.. note::

  nextflow parameters have only one ``-`` before parameter names. Pipeline parameters
  will always have ``--`` in front of them. Nextflow commands, like ``run, info, log, ...``
  don't have any ``-`` in front of them

Cleanup
~~~~~~~

After a pipeline is completed with success, it's better to clean up ``work`` directory
in order to save space. All the desired outputs **need to be saved outside** this folder,
in order to safely remove temporary data. There's a nextflow
`clean <https://www.nextflow.io/docs/latest/cli.html#clean>`_ option which safely
remove temporary files and nextflow logs. You can have information on nextflow runs
by calling ``nextflow log`` inside your project folder::

  $ nextflow log
  TIMESTAMP               DURATION        RUN NAME                STATUS  REVISION ID     SESSION ID                              COMMAND
  2021-01-14 18:31:18     34m 17s         magical_roentgen        OK      3643a94411      fa1714cf-1dbf-45ec-9910-9dcb27aab52b    nextflow run nf-core/rnaseq -profile test,singularity -resume --max_cpus=24
  2021-01-15 15:38:02     -               magical_rosalind        -       3643a94411      fa1714cf-1dbf-45ec-9910-9dcb27aab52b    nextflow run nf-core/rnaseq -profile test,singularity -resume --max_cpus=24

Then you could remove a specific run using name, for example::

  $ nextflow clean magical_roentgen -f

See `nextflow clean <https://www.nextflow.io/docs/latest/reference/cli.html#clean>`_
documentation for more info.

.. note::

  When calling log, you can inspect the command line used to execute the pipeline.
  You could also get information about execution times. For more information, take a look at
  `nextflow log <https://www.nextflow.io/docs/latest/reference/cli.html#log>`_ documentation.

.. hint::

  Despite singularity will write images in ``$NXF_SINGULARITY_CACHEDIR``, there are
  also cache files stored inside your ``$HOME/.singularity/cache`` directory.
  Free some space with::

    $ singularity cache clean

  The previous command will not affect your downloaded singularity images in
  ``$NXF_SINGULARITY_CACHEDIR`` folder. If you want to remove them, you have to
  do it manually. See :ref:`Clean up Singularity <clean-up-singularity>` section
  of this guidelines for more information.

.. warning::

  calling ``nextflow clean -f`` without *sessionid*, or *run name* will only remove
  temporary files from the last nextflow run, without removing files from other previous sessions.
  If you want to remove **ALL** your nextflow cache directories with a single command,
  you can do::

    $ nextflow clean $(nextflow log -q) -f

  where ``nextflow log -q`` simply returns only *run name* for all your nextflow
  run in your working folder.

Update a pipeline
~~~~~~~~~~~~~~~~~

.. _update-a-pipelines:

If you manage community pipeline using ``nextflow`` or ``nf-core`` software (not using ``git``),
you can have information on outdated pipelines with ``nf-core pipelines list`` command::

  $ nf-core pipelines list
  ┏━━━━━━━━━━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━┓
  ┃ Pipeline Name     ┃ Stars ┃ Latest Release ┃      Released ┃  Last Pulled ┃ Have latest release? ┃
  ┡━━━━━━━━━━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━┩
  │ rnaseq            │   323 │            3.1 │   2 weeks ago │  2 hours ago │ Yes (v3.1)           │
  │ methylseq         │    66 │          1.6.1 │   3 weeks ago │ 4 months ago │ No (v1.5)            │

In this example, we can see that the ``rnaseq`` pipeline is just updated, while
``methylseq`` is quite old and need to be updated.

.. hint::

  You can search for as specific pipeline with ``nf-core pipelines list <pattern>``, for example::

    $ nf-core pipelines list rnaseq

.. note::

  When you manage pipelines using nextflow software, pipelines are locally downloaded
  in your ``$HOME/.nextflow/assets/`` (see :ref:`Manage community pipelines with nf-core<manage-community-pipelines>`):
  the information you see reflect the updates of the community pipelines
  compared to your local assets.

In order to update a community pipeline, you need to call ``nextflow pull``, for
example::

  $ nextflow pull nf-core/rnaseq

this will update your local assets by downloading the latest default revision of
the pipeline. If you need a specific version (or branch), you need to specify it
with ``-r`` option::

  $ nextflow pull nf-core/rnaseq -r 3.12

.. tip::

  You can get a list of available revision and version with::

    $ nextflow info nf-core/rnaseq

  This is related to the local copy of the pipeline in your assets folder, make
  sure to do this after a ``nextflow pull`` command to collect the latest
  information.

.. hint::

  the same considerations apply with custom shared pipelines, for example::

    $ nextflow pull cnr-ibba/nf-resequencing-mem -r issue-1

.. warning::

  if you download a specific version with ``nextflow pull``, you have to specify
  it when you call ``nextflow run`` with the same ``-r`` option. This is required
  if you need to run your analyses with an old pipeline version, or if your ``nextflow``
  executable doesn't support the latest pipeline version.

Delete the local copy of a pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to remove a local copy of a pipeline (a pipeline installed in your cache
using ``nextflow pull`` or ``nextflow run``), simply type::

  $ nextflow drop <pipeline_name>

where ``<pipeline_name>`` is a single row returned ``nextflow list`` (*github
organization/pipeline name*)
