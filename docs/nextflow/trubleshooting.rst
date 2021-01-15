
Trubleshooting
==============

Failed to pull singularity image
--------------------------------

Sometimes singularity cannot download an image from https://quay.io/. In such case,
nextflow will raise an error and will stop the execution like this::

  Error executing process > 'RNASEQ:QUANTIFY_SALMON:SALMON_SE_TRANSCRIPT (salmon_tx2gene.tsv)'

  Caused by:
    Failed to pull singularity image
    command: singularity pull  --name quay.io-biocontainers-bioconductor-summarizedexperiment-1.18.1--r40_0.img.pulling.1610634041691 docker://quay.io/biocontainers/bioconductor-summarizedexperiment:1.18.1--r40_0 > /dev/null
    status : 255
    message:
      INFO:    Converting OCI blobs to SIF format
      INFO:    Starting build...
      Getting image source signatures
      Copying blob sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
      Copying blob sha256:77c6c00e8b61bb628567c060b85690b0b0561bb37d8ad3f3792877bddcfe2500
      Copying blob sha256:3aaade50789a6510c60e536f5e75fe8b8fc84801620e575cb0435e2654ffd7f6
      Copying blob sha256:00cf8b9f3d2a08745635830064530c931d16f549d031013a9b7c6535e7107b88
      Copying blob sha256:7ff999a2256f84141f17d07d26539acea8a4d9c149fefbbcc9a8b4d15ea32de7
      Copying blob sha256:d2ba336f2e4458a9223203bf17cc88d77e3006d9cbf4f0b24a1618d0a5b82053
      Copying blob sha256:dfda3e01f2b637b7b89adb401f2f763d592fcedd2937240e2eb3286fabce55f0
      Copying blob sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
      Copying blob sha256:10c3bb32200bdb5006b484c59b5f0c71b4dbab611d33fca816cd44f9f5ce9e3c
      Copying blob sha256:f981c3bfe61f7355e034d40b620e60aefc6b272a8d0ac10fa9e1892bb6b17b56
      Copying config sha256:ff870dedc9d11d9622344d7a4ff0c0c25a890f2233a84926b6cb0e67f422500e
      Writing manifest to image destination
      Storing signatures
      FATAL:   While making image from oci registry: error fetching image to cache: while building SIF from layers: conveyor failed to get: no descriptor found for reference "70c154f9aee9152d9e03c474cd4b5e5eee5856cda5b62c46b10c4ae7932e763d"

In such cases, you can solve those errors by manually download the singularity image
into ``$NXF_SINGULARITY_CACHEDIR`` cache directory. Track the failed ``command`` line
in nextflow output, then move in ``$NXF_SINGULARITY_CACHEDIR`` directory and call
such command manually. After downloading the image, rename the file and remove the
``.pulling.[0-9]*`` from the image name (nextflow images should end with ``.img``
extension). For example in the previous case::

  $ cd $NXF_SINGULARITY_CACHEDIR
  $ singularity pull  --name quay.io-biocontainers-bioconductor-summarizedexperiment-1.18.1--r40_0.img.pulling.1610634041691 docker://quay.io/biocontainers/bioconductor-summarizedexperiment:1.18.1--r40_0 > /dev/null
  $ mv quay.io-biocontainers-bioconductor-summarizedexperiment-1.18.1--r40_0.img.pulling.1610634041691 quay.io-biocontainers-bioconductor-summarizedexperiment-1.18.1--r40_0.img

After that, you could resume your nextflow pipeline by adding the ``-resume`` option
in your command line in order using the cached results of the previous calculations

.. note::

  nextflow singularity containers are moving from `quay <https://quay.io/>`__ to
  `depot.galaxyproject.org <https://depot.galaxyproject.org/singularity/>`__:
  the latter seems to have better downloading performance

.. _nextflow-version-required:

Nextflow version does't match the required version
------------------------------------------------------

It is possible that when running a pipeline with nextflow, you will get a error
like this::

  Nextflow version 20.10.0 does not match workflow required version: >=20.11.0-edge

Is such case, you have two options. The first is to execute a previous version of
the pipeline that is compatible with your nextflow version. You can have information
on version on `nf-core pipeline <https://nf-co.re/pipelines>`__ or directly
from the GitHub project of `nf-core <https://github.com/nf-core>`__ organization.
Once you find your desidered version, you have to declare it with the parameter
``-r`` when calling nextflow, for example::

  $ nextflow run nf-core/rnaseq -r 2.0 -profile test,singularity -resume

The second option is to upgrade your nextflow version. You can install a specific
version of nextflow from the `nextflow release page <https://github.com/nextflow-io/nextflow/releases>`__
Copy the nextflow asset link present in every release, and the install nextflow like
this::

  $ wget -qO- https://github.com/nextflow-io/nextflow/releases/download/v20.12.0-edge/nextflow-20.12.0-edge-all | bash

This will download all the requirements and will put nextflow in your current directory.
Change the nextflow default permissions to ``755`` and move such executable in a
directory with a higher position in your ``$PATH`` environment, for example ``$HOME/bin``
