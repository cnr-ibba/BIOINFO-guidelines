
Singularity
===========

.. contents:: Table of Contents

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
^^^^^^^^^^

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
^^^^^^^^^^^^^^^^^^^^^^^^^^^

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
~~~~~~~~~~~~~~~~~~~~~~~~~

The community behind the Apptainer development wants to minimize the differences
between Apptainer and singularity: for example the `*.sif` images should work
in both environments. Even the environments variables should work with both software,
where Apptainer can read Singularity environments variables if not defined. Moreover,
Apptainer will have a symlink to the ``apptainer`` executable named ``singularity``:
this means that all the ``singularity`` commands will be executed with the proper
executable. See `Singularity Compatibility <https://apptainer.org/docs/user/main/singularity_compatibility.html>`_
documentation for more information.

Docker and Singularity
^^^^^^^^^^^^^^^^^^^^^^

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
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Singularity can pull containers directly from Docker Hub.

You can search for containers on `Docker Hub <https://hub.docker.com/>`_.
Once you find a suitable container, use Singularity to pull it:

.. code-block:: bash

  singularity pull [container_name.sif] docker://<dockerhub-user>/<container-name>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using Sylabs Cloud
^^^^^^^^^^^^^^^^^^

Sylabs provides a cloud platform for Singularity containers, and it’s a common
replacement for Singularity Hub. Visit `Sylabs Cloud`_
to search for containers. To pull a container from Sylabs Cloud:

.. code-block:: bash

  singularity pull [container_name.sif] library://<user>/<collection>/<container>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using Biocontainers
^^^^^^^^^^^^^^^^^^^

Biocontainers is a community-driven project that provides bioinformatics software
in containers. You can search for bioinformatics containers on the
`Biocontainers <https://biocontainers.pro/>`_ website. To pull a container from
Biocontainers:

.. code-block:: bash

  singularity pull [container_name.sif] docker://quay.io/biocontainers/<container-name>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file
name of the downloaded container.

Using docker-daemon
^^^^^^^^^^^^^^^^^^^

Sometimes you may have a Docker container already pulled on your system, or you
have just created a docker image and you want to convert it to a Singularity container:
you can use the `docker-daemon` URI to pull the image from the local Docker daemon:

.. code-block:: bash

  singularity pull [container_name.sif] docker-daemon:<docker-image>:<tag>

Where ``container_name.sif`` is an optional parameters which set the output file.

.. warning::

  Please note that when using the `docker-daemon` URI, you don't need to specify
  ``docker-daemon://`` but just ``docker-daemon:`` followed by the image id.

Using mulled-search
^^^^^^^^^^^^^^^^^^^

mulled-search is p part of the [galaxy-tool-util](https://pypi.org/project/galaxy-tool-util/)
that allows you to search for bioinformatics software containers in the Bioconda
and Biocontainers repositories. To search for a container using mulled-search,
you should specify the destination (e.g., quay) and the software you are looking for,
for example:

.. code-block:: bash

  mulled-search --destination quay singularity -s bwa samtools

When searching for more than one software in the same time, mulled-search will
returns also mulled containers, which are containers that have multiple software
installed in the same container. Since is not trivial to understand software
versions in mulled containers, there's another tool in the galaxy-tool-util
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
  See :ref:`Setting NXF_SINGULARITY_CACHEDIR <set-singularity-cache>` for more information

.. _Sylabs Cloud: https://cloud.sylabs.io/library

Create a container
------------------

Build a container
^^^^^^^^^^^^^^^^^

Build a container without root access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create a mulled container
^^^^^^^^^^^^^^^^^^^^^^^^^
