
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
Apptainer will have a symlink to the `apptainer` executable named `singularity`:
this means that all the `singularity` commands will be executed with the proper
executable. See `Singularity Compatibility <https://apptainer.org/docs/user/main/singularity_compatibility.html>`_
documentation for more information.

Docker and Singularity
^^^^^^^^^^^^^^^^^^^^^^

Searching for a container
-------------------------

* singularity hub
* Biocontainers
* Docker Hub
* Quay.io
* Docker Daemon
* Mulled-search

Create a container
------------------

Build a container
^^^^^^^^^^^^^^^^^

Build a container without root access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
