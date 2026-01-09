
Singularity
===========

.. contents:: Table of Contents

.. _about-singularity:

About Singularity
-----------------

**Singularity** is a free, cross-platform, and open-source computer program for
virtualization. It is used to create reproducible and portable software containers
for scientific computing and high-performance computing (HPC).
Reproducibility and portability imply the ability to move containers from system
to system (e.g., a new machine). With Singularity containers, developers can work
in customized, reproducible environments that can be copied and executed on other platforms.
You can refer to the `Singularity`_ official documentation.

.. _Singularity: https://docs.sylabs.io/guides/latest/user-guide/

Containers
----------

Containers are single files that allow the transfer of computing environments
without worrying about installing all needed software and dependencies on each different OS or machine.
Containers are very useful for **reproducible science**: Singularity containers
include all programs, libraries, data, and scripts for a specific scientific problem,
and can then be archived or distributed for replication, regardless of the hardware
architecture or OS used.

Singularity containers are similar to Docker containers, but they are designed for HPC environments:
since Singularity containers do not require root access to run, they are more secure
and easier to use in HPC environments.

Singularity containers can be used to run applications, workflows, and entire operating systems.
They can be used to run software that is not available on the host system, or to
run software that requires a specific version of a library or tool.
Singularity containers can also be used to run software that requires a specific
version of an operating system, for example you can have a container based on a
specific version of Ubuntu, CentOS, or Debian which could be required in order
to install and run a specific software.

.. note::

  Singularity is already installed in our IBBA infrastructure, and it's available
  on our *core* machine and in every cluster *nodes*

Apptainer and SingularityCE
---------------------------

Apptainer and SingularityCE are two branches that originated from the original
Singularity project. Apptainer is the community-driven continuation of Singularity,
maintained under the Linux Foundation. It aims to provide a secure, stable, and
performant container runtime for scientific and high-performance computing.
SingularityCE (Community Edition) is maintained by Sylabs and focuses on
delivering enterprise-grade features and support. Both versions retain the core
principles of Singularity, such as ease of use, security, and compatibility with
HPC environments, but they may offer different features and updates based on their
respective development goals.

Differences between Apptainer and SingularityCE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

While both Apptainer and SingularityCE originated from the same Singularity project
and share many core principles, there are some differences between them:

- **Governance and Maintenance**: Apptainer, managed by the Linux Foundation, is
  community-driven and focuses on secure, stable, and performant container runtime
  for scientific and HPC. SingularityCE, maintained by Sylabs, aims to deliver
  enterprise-grade features and support.
- **Development Goals**: Apptainer emphasizes community contributions and open
  development, focusing on stability and security for HPC. SingularityCE focuses
  on enterprise features, commercial support, and may include proprietary enhancements.
- **Features and Updates**: Apptainer prioritizes features and updates benefiting
  the scientific and HPC community, driven by community needs. SingularityCE offers
  features and updates tailored for enterprise users, focusing on commercial use cases.
- **Support and Documentation**: Apptainer relies on community support and contributions,
  with resources provided by the community and the Linux Foundation. SingularityCE
  provides enterprise-level support and documentation, with resources offered by Sylabs.
- **Licensing**: Apptainer is licensed under the Apache License 2.0, allowing free
  use, modification, and distribution. SingularityCE's licensing may vary, with
  some components potentially proprietary.
- **Compatibility**: Apptainer is designed to be compatible with Singularity containers
  and workflows, maintaining compatibility with existing features. SingularityCE
  may introduce new features that are not backward-compatible with older versions.

Please see `this discussion <https://github.com/sylabs/singularity/discussions/2948>`_
for more information regarding Apptainer and SingularityCE.

Singularity compatibility
-------------------------

The community behind the Apptainer development wants to minimize the differences
between Apptainer and singularity: for example the `*.sif` images should work
in both environments. Even the environments variables should work with both software,
where Apptainer can read Singularity environments variables if not defined. Moreover,
Apptainer will have a symlink to the ``apptainer`` executable named ``singularity``:
this means that all the ``singularity`` commands will be executed with the proper
executable. See `Singularity Compatibility <https://apptainer.org/docs/user/main/singularity_compatibility.html>`_
documentation for more information.

Docker and Singularity
~~~~~~~~~~~~~~~~~~~~~~

Docker and Singularity are both popular containerization technologies, but they
serve different purposes and environments. Docker is widely used in software development
for creating, deploying, and managing containers in a variety of environments,
including cloud and local development setups. It requires root privileges to run,
which can pose security risks in multi-user environments. Singularity, on the
other hand, is designed specifically for high-performance computing (HPC) and
scientific workloads. It does not require root access to run containers, making
it more secure for shared computing environments. Both Docker and Singularity
allow for the creation of portable and reproducible environments, but Singularity's
focus on security and compatibility with HPC systems sets it apart from Docker's
broader application scope.

Singularity and Docker Integration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Singularity provides seamless integration with Docker, allowing users to leverage
Docker images without needing Docker installed on their systems. This integration
offers several benefits:

- **No Docker Installation Required**: You can use Docker images with Singularity
  without having Docker installed on your system.
- **Shell Access**: Singularity allows you to shell into a Docker image that has
  been converted into a Singularity container.
- **Instant Execution**: You can run a Docker image instantly as a Singularity
  container, providing quick access to the software environment.
- **Pulling Docker Images**: Singularity can pull Docker images directly from Docker
  Hub without requiring sudo privileges.
- **Building from Docker Layers**: You can build Singularity images using bases
  from assembled Docker layers, which include the environment, guts, and labels
  defined in the Docker image.

These features make it easy to use Docker images in high-performance computing (HPC)
environments where Singularity is preferred for its security and compatibility.
For more information, please see the
`Singularity and Docker <https://singularity-userdoc.readthedocs.io/en/latest/singularity_and_docker.html>`_
documentation.

Searching for a container
-------------------------

Singularity Hub (SHub) was previously a platform where users could store and share
Singularity containers. However, Singularity Hub is no longer actively maintained
as of April 2021. Instead, Singularity users now commonly use container registries
like `Docker Hub <https://hub.docker.com/>`_ or `Sylabs Cloud`_
to host and search for Singularity containers.

To search for a Singularity container, follow these steps depending on the platform:

Using Docker Hub with Singularity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Singularity can pull containers directly from Docker Hub.

You can search for containers on `Docker Hub <https://hub.docker.com/>`_.
Once you find a suitable container, use Singularity to pull it:

.. code-block:: bash

  singularity pull [container_name.sif] docker://<dockerhub-user>/<container-name>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using Sylabs Cloud
~~~~~~~~~~~~~~~~~~

Sylabs provides a cloud platform for Singularity containers, and it’s a common
replacement for Singularity Hub. Visit `Sylabs Cloud`_
to search for containers. To pull a container from Sylabs Cloud:

.. code-block:: bash

  singularity pull [container_name.sif] library://<user>/<collection>/<container>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using Biocontainers
~~~~~~~~~~~~~~~~~~~

Biocontainers is a community-driven project that provides bioinformatics software
in containers. You can search for bioinformatics containers on the
`Biocontainers <https://biocontainers.pro/>`_ website. To pull a container from
Biocontainers:

.. code-block:: bash

  singularity pull [container_name.sif] docker://quay.io/biocontainers/<container-name>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using docker-daemon
~~~~~~~~~~~~~~~~~~~

Sometimes you may have a Docker container already pulled on your system, or you
have just created a docker image and you want to convert it to a Singularity container:
you can use the `docker-daemon` URI to pull the image from the local Docker daemon:

.. code-block:: bash

  singularity pull [container_name.sif] docker-daemon:<docker-image>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file.

.. warning::

  Please note that when using the `docker-daemon` URI, you don't need to specify
  ``docker-daemon://`` but just ``docker-daemon:`` followed by the image id.

.. _using-mulled-search:

Using mulled-search
~~~~~~~~~~~~~~~~~~~

mulled-search is part of the `galaxy-tool-util <https://pypi.org/project/galaxy-tool-util/>`_
that allows you to search for bioinformatics software containers in the Bioconda
and Biocontainers repositories. To search for a container using mulled-search,
you should specify the destination (e.g., quay) and the software you are looking for,
for example:

.. code-block:: bash

  mulled-search --destination quay singularity -s bwa samtools

When searching for more than one software in the same time, mulled-search will
returns also mulled containers, which are containers that have multiple software
installed in the same container. Since is not trivial to understand software
versions in mulled containers, there's another tool in the ``galaxy-tool-util``
to determine the container *hash* of the desired software:

.. code-block:: bash

  mulled-hash bwa=0.7.17,samtools=1.19.2

This will return the hash of the container that contains the specified software
versions. You can use this hash to filter out the desired url from the mulled-search:

.. code-block:: bash

  mulled-search --destination singularity -s bwa samtools | \
    grep $(mulled-hash bwa=0.7.17,samtools=1.19.2)

The returned url can be used to pull the container with singularity:

.. code-block:: bash

  singularity pull bwa_samtools.sif \
    https://depot.galaxyproject.org/singularity/mulled-v2-fe8faa35dbf6dc65a0f7f5d4ea12e31a79f73e40:a34558545ae1413d94bde4578787ebef08027945-0

.. note::

  The `mulled-search` tool is already installed in our shared infrastructure at IBBA.

Using a Local Singularity Image
-------------------------------

If you have a `.sif` container locally, you can run it directly with Singularity:

.. code-block:: bash

  singularity run <container-name>.sif

This means also that you could copy a pulled container to a different machine and be
able to run a singularity container.

.. hint::

  In our shared infrastructure at IBBA, we have a shared folder directory in which
  we put singularity container managed with nextflow: those containers are downloaded
  by nextflow but can be used like any other pulled singularity container.
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR <set-nxf-singularity-cache>` for more information

.. _Sylabs Cloud: https://cloud.sylabs.io/library

Create a container
------------------

Building your own Singularity container allows you to create custom environments
tailored to your specific needs. There are several methods to create Singularity
containers, depending on whether you have root access and what base you want to
start from.

Build a container with a definition file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The recommended way to build a Singularity container is using a *definition file*
(also called a *recipe*). A definition file is a text file that contains all the
instructions to build a container, including the base image, software installations,
environment variables, and scripts.

Here's a basic example of a Singularity definition file:

.. code-block:: singularity

  Bootstrap: docker
  From: ubuntu:22.04

  %post
      apt-get update && apt-get install -y \
          python3 \
          python3-pip \
          wget
      pip3 install numpy pandas

  %environment
      export LC_ALL=C

  %runscript
      echo "Container was created $NOW"
      echo "Arguments received: $*"
      exec python3 "$@"

  %labels
      Author your.name@example.com
      Version v1.0.0

To build a container from this definition file (assuming you have root/sudo access):

.. code-block:: bash

  sudo singularity build mycontainer.sif mycontainer.def

Where ``mycontainer.def`` is your definition file and ``mycontainer.sif`` is the
output container image.

.. note::

  Describing in detail the sections of a singularity definition file is out of the
  scope of this documentation. Please refer to the `Singularity Definition File`_
  documentation for more information.

.. _`Singularity Definition File`: https://docs.sylabs.io/guides/main/user-guide/definition_files.html#definition-files

Build a container without root access
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you don't have root access on your system, you have several options:

**Using --fakeroot** (if available):

On systems where the ``--fakeroot`` option is enabled, you can build containers
without actual root privileges:

.. code-block:: bash

  singularity build --fakeroot mycontainer.sif mycontainer.def

**Using Remote Builder**:

Sylabs provides a remote build service that allows you to build containers in the
cloud. First, you need to create an account and generate an access token at
`Sylabs Cloud`_. Copy the token into your clipboard, then login to the remote builder
with:

.. code-block:: bash

  singularity remote login

And paste your token when prompted. Then, you can build remotely with:

.. code-block:: bash

  singularity build --remote mycontainer.sif mycontainer.def

.. note::

  Adding external files to a singularity container when using the remote builder
  is not supported with older Singularity versions (i.e. before ``v3.10``). Please refer to the
  `Supporting Local Files in a Singularity Remote Build <remote-build-local-files_>`_
  documentation for more information.

.. _remote-build-local-files: https://sylabs.io/2022/06/supporting-local-files-in-a-singularity-remote-build/

.. hint::

  You can check if the remote builder is properly configured with:

  .. code-block:: bash

    singularity remote list

  You can inspect your builds and containers on the
  `Sylabs Cloud Dashboard <https://cloud.sylabs.io/dashboard#builds>`_ web interface.

.. warning::

  Tokens are sensitive information: avoid sharing them or committing them
  to public repositories.

Singularity best practices
---------------------------

When working with Singularity containers, following best practices ensures
reproducibility, efficiency, and ease of use.

.. _set-singularity-cache:

Set SINGULARITY_CACHEDIR
~~~~~~~~~~~~~~~~~~~~~~~~

Although Singularity uses a default cache directory (``~/.singularity``),
it's a good practice to set the ``SINGULARITY_CACHEDIR`` environment variable
to a specific location where you want Singularity to store its cache files. This
is especially useful when using Nextflow, in particular when downloading pipelines
using ``nf-core pipelines download`` or when executing a pipeline for the first time:
this will reuse the same singularity cache
folder when downloading containers. To set the cache directory, add the following line
to your shell configuration file (e.g., ``~/.bashrc`` or ``~/.profile``):

.. code-block:: bash

  export SINGULARITY_CACHEDIR=/path/to/your/singularity/cache

Remember to replace ``/path/to/your/singularity/cache`` with the actual path
where you want to store the Singularity cache. If in doubt, you can use the
default path ``$HOME/.singularity``, singularity will create the ``cache`` subdirectory
automatically.

.. note::

  The singularity cache directory is used to store downloaded layers
  and other temporary files that speed up subsequent operations: this is not
  the location where singularity containers are stored when you pull them with
  ``singularity pull`` command. Cleaning up the cache directory can help free up disk space
  when needed. See :ref:`Clean up cache <clean-up-singularity>` for more information.

.. hint::

  When using Nextflow, you can also set the singularity cache directory
  by defining the ``NXF_SINGULARITY_CACHEDIR`` environment variable: this will
  be the path where Nextflow will look for singularity containers, and can be
  reused across different pipelines and by calling ``singularity run`` directly
  on the downloaded containers.
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR <set-nxf-singularity-cache>` for more information.

Use specific versions
~~~~~~~~~~~~~~~~~~~~~

Always specify exact versions for your base images and software packages. Instead of:

.. code-block:: singularity

  Bootstrap: docker
  From: ubuntu:latest

Use:

.. code-block:: singularity

  Bootstrap: docker
  From: ubuntu:22.04

This ensures that your container will be reproducible in the future, even if the
``latest`` tag points to a different version.

Keep containers immutable
~~~~~~~~~~~~~~~~~~~~~~~~~

Once a container is built, avoid modifying it. If you need to make changes, create
a new version of the container instead. This ensures reproducibility and makes it
easier to track changes over time.

Document your containers
~~~~~~~~~~~~~~~~~~~~~~~~

Use the ``%labels`` section in your definition file to document important information
about your container:

.. code-block:: singularity

  %labels
      Author your.name@example.com
      Version v1.0.0
      Description "Container for RNA-seq analysis"
      Date 2026-01-08

You can view these labels later with:

.. code-block:: bash

  singularity inspect mycontainer.sif

Minimize container size
~~~~~~~~~~~~~~~~~~~~~~~

Keep your containers as small as possible by:

- Using minimal base images (e.g., ``alpine``, ``debian:slim``)
- Cleaning up package manager caches after installation
- Removing unnecessary files and documentation

Example:

.. code-block:: singularity

  %post
      apt-get update && apt-get install -y \
          python3 \
          python3-pip \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/*

Use bind mounts for data
~~~~~~~~~~~~~~~~~~~~~~~~

Never include large datasets inside your containers. Instead, use bind mounts to
access data from the host system:

.. code-block:: bash

  singularity run -B /path/to/data:/data mycontainer.sif

This keeps your containers small and portable while still allowing access to the
data you need.

.. _clean-up-singularity:

Clean up cache
~~~~~~~~~~~~~~

Singularity caches downloaded images and layers to speed up subsequent operations.
Over time, this cache can grow quite large. To clean up the Singularity cache:

**Check cache size**:

.. code-block:: bash

  du -sh ~/.singularity/cache

**Clean all cache**:

.. code-block:: bash

  singularity cache clean

**Clean specific cache types**:

.. code-block:: bash

  # Clean only Docker layers
  singularity cache clean --type blob

  # Clean only pulled images
  singularity cache clean --type library

**Force clean without confirmation**:

.. code-block:: bash

  singularity cache clean --force

.. warning::

  Cleaning the cache will remove all cached images and layers. This means that
  subsequent pulls or builds will need to download everything again. Only clean
  the cache when you're sure you won't need the cached files or when disk space
  is limited.
