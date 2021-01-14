
Running Nextflow
================

.. contents:: Table of Contents

A note of profiles
------------------

Despite nextflow could be run using :doc:`conda <../terminal/conda>`,
:doc:`singularity <../terminal/singularity>`, :doc:`docker <../terminal/docker>`
or other profiles, the recommended profile tu use is **singularity**: this solution
in fact manage all software dependencies in a unique file and could be cached and
reused in order to speed up the calculation process

.. warning::

  Downloading software dependencies could take a lot of time and could be subject
  to networking errors, which are not related to pipelines or data but can slow or
  broke pipeline execution. In such way, it's better to configure **caches** when
  downloading softwares: singularity cache could be configured in
  `singularity scope <https://www.nextflow.io/docs/edge/config.html#scope-singularity>`__
  or better using ``$NXF_SINGULARITY_CACHEDIR``.
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR<set-singularity-cache>` for more information

Executing a community pipeline
------------------------------

Nexflow lets to build and share bioinformatics pipelines accross the community. The
simples way to use nextflow is to identify the pipeline you need, check for its requirements
and then launch it using your data. Since all the nextflow comminity pipelines
are public, you could download and modify them according your needs.

Search for a community pipeline
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Community pipelines are available at `nf-core pipeline <https://nf-co.re/pipelines>`__
section: you could search a pipeline and browse its documentation in the nf-core site.
For example, by searching for ``rnaseq`` you could reach `rnaseq pipeline <https://nf-co.re/rnaseq>`__
page project a documentation on usage by clicking on `Usage docs <https://nf-co.re/rnaseq/usage>`__ menu.
In order to download the pipeline, softwares and testing all in your local environment
you can call directly nextflow, for example for the *rnaseq* pipeline::

  $ mkdir nf-rnaseq
  $ cd nf-rnaseq
  $ nextflow run nf-core/rnaseq -profile test,singularity -resume

.. hint::

  Calling ``nextflow run`` with a remote pipeline will place the ``work`` and
  ``results`` directories in the current working directory. For such reason, it's
  better to create an empty project directory in which calling ``nextflow run``

.. warning::

  It is possible that the nextflow version required by the pipeline is different
  from your nextflow version installed and you couldn't execute the pipeline. Please
  see :ref:`this section <nextflow-version-required>` of nextflow trubleshooting.

Manage community pipelines with ``nf-core``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

See :ref:`Install nf-core/tools <install-nf-core>` to get ``nf-core/tools`` software
installed

Section 2.2 Title
-----------------
