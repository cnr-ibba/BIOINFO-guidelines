
Running Nextflow
================

.. contents:: Table of Contents

A note on profiles
------------------

Despite nextflow could be run using :doc:`conda <../terminal/conda>`,
:doc:`singularity <../terminal/singularity>`, :doc:`docker <../terminal/docker>`
or other profiles, the recommended profile to use is **singularity**: this solution
in fact manages all software dependencies in a unique file and could be cached and
reused in order to speed up the calculation process

.. warning::

  Downloading software dependencies could take a lot of time and could be subject
  to networking errors, which are not related to pipelines or data but can slow or
  broke pipeline execution. In such way, it's better to configure **caches** when
  downloading softwares: singularity cache could be configured in
  `singularity scope <https://www.nextflow.io/docs/edge/config.html#scope-singularity>`__
  or better using ``$NXF_SINGULARITY_CACHEDIR``.
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR<set-singularity-cache>` for more information

Execute a community pipeline
----------------------------

Nextflow lets to build and share bioinformatics pipelines across the community. The
simples way to use nextflow is to identify the pipeline you need, check for its requirements
and then launch it using your data. Since all the nextflow community pipelines
are public, you could download and modify them according your needs.

Search for a community pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Community pipelines are available at `nf-core pipeline <https://nf-co.re/pipelines>`__
section: you could search a pipeline and browse its documentation in the `nf-core <https://nf-co.re/>`__ site.
For example, by searching for ``rnaseq`` you could reach the `rnaseq pipeline <https://nf-co.re/rnaseq>`__
page project and get documentation on its usage by clicking on `Usage docs <https://nf-co.re/rnaseq/usage>`__ menu.
In order to download the pipeline, the softwares, and testing all in your local environment,
you can call directly nextflow, for example for the *rnaseq* pipeline::

  $ mkdir nf-rnaseq
  $ cd nf-rnaseq
  $ nextflow run nf-core/rnaseq -profile test,singularity -resume

.. note::

  The community pipelines have a ``--help`` option to show all supported parameters.
  try::

    $ nextflow run nf-core/rnaseq --help

  To get a full list of the available options

.. hint::

  Calling ``nextflow run`` with a remote pipeline will place the ``work`` and
  ``results`` directories in the current working directory. For such reason, it's
  better to create an empty project directory in which calling ``nextflow run``

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

  $ nextflow pull nf-core/rnaseq -r 3.0

.. warning::

  Whenever you pull a pipeline version different from the latest, you **MUST** declare
  the same version or branch when calling nextflow, for example::

    $ nextflow run nf-core/rnaseq -r 3.0 --help

  If you need to update your local pipeline to latest version see the
  :ref:`Update a pipeline <update-a-pipelines>` section.

Manage community pipelines with ``nf-core``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _manage-community-pipelines:

Whenever you run a community pipeline, nextflow will download and cache it (in
your ``$HOME/.nextflow/assets/`` folder). You could check your installed community pipelines
with::

  $ nf-core list

You could search for a specific pipeline by providing a name as an argument::

  $ nf-core list rna

The most interesting thing is the possibility to configure params with::

  $ nf-core launch rnaseq

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

  $ nextflow run cnr-ibba/nf-resequencing-mem -resume -profile singularity --reads_path "reads/*_R{1,2}_*.fastq.gz" --genome_path genome.fa

where `cnr-ibba/nf-resequencing-mem <https://github.com/cnr-ibba/nf-resequencing-mem>`__
is the repository which contains the nextflow pipeline.

.. tip::

  You can configure nextflow to store your GitHub access credentials, see
  :ref:`Access to private repositories <nextflow-private-repo>` of this guidelines

Nextflow best-practices
-----------------------

Here are some tips that could be useful while running nextflow.

Resume calculations
~~~~~~~~~~~~~~~~~~~

Nextflow, by default, executes every calculation in a subfolder inside the
``work`` directory in your current working directory. Every steps is executed in
separate subfolders and nextflow will take care about *inputs* and *outputs* among
related steps. It is frequent to call nextflow multiple times, for example while
modifying a pipeline. In such way, you can save a lot of spaces (and calculation times)
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
`clean <https://www.nextflow.io/docs/latest/cli.html#clean>`__ option which safely
remove temporary files and nextflow logs. You can have information on nextflow runs
by calling ``nextflow info`` inside your project folder::

  $ nextflow log
  TIMESTAMP               DURATION        RUN NAME                STATUS  REVISION ID     SESSION ID                              COMMAND
  2021-01-14 18:31:18     34m 17s         magical_roentgen        OK      3643a94411      fa1714cf-1dbf-45ec-9910-9dcb27aab52b    nextflow run nf-core/rnaseq -profile test,singularity -resume --max_cpus=24
  2021-01-15 15:38:02     -               magical_rosalind        -       3643a94411      fa1714cf-1dbf-45ec-9910-9dcb27aab52b    nextflow run nf-core/rnaseq -profile test,singularity -resume --max_cpus=24

Then you could remove a specific run using name, for example::

  $ nextflow clean magical_roentgen -f

See `nextflow clean <https://www.nextflow.io/docs/latest/cli.html#clean>`__
documentation for more info.

.. note::

  When calling log, you can inspect the command line used to execute the pipeline.
  You could also get information about execution times. For more information, take a look at
  `nextflow log <https://www.nextflow.io/docs/latest/cli.html#log>`__ documentation.

.. hint::

  Despite singularity will write images in ``$NXF_SINGULARITY_CACHEDIR``, there are
  also cache files stored inside your ``$HOME/.singularity/cache`` directory.
  Free some space with::

    $ singularity cache clean

  The previous command will not affect your downloaded singularity images in
  ``$NXF_SINGULARITY_CACHEDIR`` folder. If you want to remove them, you have to
  do it manually.

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

If you manage community pipeline using ``nextflow`` or ``nf-core`` script (not using ``git``),
you can have information on outdated pipelines with ``nf-core list`` command::

  $ nf-core list
  ┏━━━━━━━━━━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━┓
  ┃ Pipeline Name     ┃ Stars ┃ Latest Release ┃      Released ┃  Last Pulled ┃ Have latest release? ┃
  ┡━━━━━━━━━━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━┩
  │ rnaseq            │   323 │            3.1 │   2 weeks ago │  2 hours ago │ Yes (v3.1)           │
  │ methylseq         │    66 │          1.6.1 │   3 weeks ago │ 4 months ago │ No (v1.5)            │

In this example, we can see that the ``rnaseq`` pipeline is just updated, while
``methylseq`` is quite old and need to be updated.

.. note::

  when you manage pipelines using nextflow software, pipelines are locally downloaded
  in your ``$HOME/.nextflow/assets/`` (see :ref:`Manage community pipelines with nf-core<manage-community-pipelines>`):
  the information you see reflect the updates of the community pipelines
  compared to your local assets.

In order to update a community pipeline, you need to call ``nextflow pull``, for
example::

  $ nextflow pull nf-core/rnaseq

this will update your local assets by downloading the latest default revision of
the pipeline. If you need a specific version (or branch), you need to specify it
with ``-r`` option::

  $ nextflow pull nf-core/rnaseq -r 3.0

.. tip::

  You can get a list of available revision and version with::

    $ nextflow info nf-core/rnaseq

.. hint::

  the same considerations apply with custom shared pipelines, for example::

    $ nextflow pull cnr-ibba/nf-resequencing-mem -r issue-1

.. warning::

  if you download a specific version with ``nextflow pull``, you have to specify
  it when you call ``nextflow run`` with the same ``-r`` option. This is required
  if you need to run your analyses with an old pipeline version, or if your ``nextflow``
  executable doesn't support the latest pipeline version.
