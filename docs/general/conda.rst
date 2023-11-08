
Conda
=====

.. contents:: Table of Contents

About Conda
-----------

Conda is a software which allows you to manage software installations in distinct
environments. It was born to support the python ecosystem, however most softwares
has been supported by conda, for example `R`_ and its packages, and there are
channels like `bioconda`_, which collect and maintain a lot of useful softwares.
The main advantage in using conda environments is that packages could be installed
directly with their dependencies, whitout the needing to compile everything. Moreover
conda and its environments can be installed by an user without administrative privileges.
Packages and dependencies are installed inside user directories, and a complete
unistallation could be done by erasing the conda installation folder.
From the `conda`_ official documentation:

.. _R: https://docs.anaconda.com/anaconda/user-guide/tasks/using-r-language/
.. _conda: https://docs.conda.io/en/latest/index.html
.. _`bioconda`: https://bioconda.github.io/

.. epigraph::

  Conda is an open source package management system and environment management
  system that runs on Windows, macOS and Linux. Conda quickly installs, runs and
  updates packages and their dependencies. Conda easily creates, saves, loads
  and switches between environments on your local computer. It was created for
  Python programs, but it can package and distribute software for any language.

Installing Conda
----------------

Is Conda already installed?
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Conda isn't installed by default on your system. However on a shared resource or
a remote machine could be already installed by the system administrator. Try to
undertstand if conda is installed using ``which``, for example::

  (base) cozzip@cloud1:~$ which conda
  /usr/local/Miniconda3-py38_4.8.3-Linux-x86_64/bin/conda
  (base) cozzip@cloud1:~$ which python
  /usr/local/Miniconda3-py38_4.8.3-Linux-x86_64/bin/python

In such case, conda is installed and currently active (The ``(base)`` near username
in the bash prompt, is the environment name currently active in the terminal)

.. hint::

  Conda is already installed and initialized in our shared **core** environment.
  When you log in you should see the ``(base)`` default environment activated.
  This installation let you use the provided environments managed by the system
  administrator, and to define your local environments in your ``$HOME`` folder.

Should I install Conda or Miniconda?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Conda is installed with a a lot of dependencies, like spyder editor, jupyter notebook
and many other packages. Miniconda is a lighter version of anaconda, which installs
only the minimal packages required to work correctly with conda. In general, you could
decide to install the whole Conda in a local installation, since in your personal computer
you could exploit the benefit of the editors and the grafical user interfaces.
When working on a remote server, using Miniconda is recommended since you have the
full control on what is installed and generally you don't need starting grafical
interfaces on a remote servers. If you are in doubt, please see the
`Anaconda or Miniconda`_ section of conda installation guide.

.. _`Anaconda or Miniconda`: https://docs.conda.io/projects/conda/en/latest/user-guide/install/download.html#anaconda-or-miniconda

Download and install Conda
~~~~~~~~~~~~~~~~~~~~~~~~~~

You could install conda or miniconda respectively `here <https://www.anaconda.com/products/individual>`__
and `here <https://docs.conda.io/en/latest/miniconda.html>`__. Then follow the
installation instructions provided by Anaconda or miniconda

Managing environments with conda
--------------------------------

Choose an environment
~~~~~~~~~~~~~~~~~~~~~

You can explore the conda environment available with::

  $ conda env list
  # conda environments:
  #
  R-4.3                    /home/cozzip/.conda/envs/R-4.3
  base                  *  /usr/local/Miniconda3-py38_4.8.3-Linux-x86_64
  nf-core                  /usr/local/Miniconda3-py38_4.8.3-Linux-x86_64/envs/nf-core

The environment with ``*`` is the current active environment. Is the same you see
in the bash prompt.

.. hint::

  ``conda env list`` is different from ``conda list`` which tells you which
  packages are installed in your current environment.

You could enable a conda environment using ``conda activate``, for example::

  $ conda activate R-4.3

You should see that the environment name near the bash prompt changed to the desidered
environment. In order to exit the current environment (and return to your previous
environment), you have to deactivate with::

  $ conda deactivate

Create a new environment
~~~~~~~~~~~~~~~~~~~~~~~~

You can create a new environment by specify the environment name using ``--name``
option. You could also specify which package to install when creating an environment::

  conda create --name <env name> [package1] [package2]

See `Managing environment <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html>`__
in conda documentation for more information

.. hint::

  You can save time by specifying package version (ex. ``python=3.8``): conda will
  have less dependencies to evaluate

A note on channels
""""""""""""""""""

.. _a-note-on-channels:

Channels are repository where conda store packages. The ``default`` contains packages
maintained by conda developers. There are others channels like `bioconda <https://bioconda.github.io/index.html>`__,
which contains a lot of bioinformatics packages, `R <https://anaconda.org/r/repo>`__,
which store *R* and its packages, `conda-forge <https://conda-forge.org/>`__, which
contains community packages, often more updated that the official channels. If you
search or want to install a package in a different channel than the ``default``, you
have to specify with the ``--channel`` option::

  $ conda search --channel R r-base=4.3
  $ conda create --channel R --name R-4.3 r-base=4.3

You can find more information on `Managing channels <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html>`__
in conda documentation.

.. warning::

  different channels could have different dependencies: for example could be difficult
  install both ``rstudio`` package from ``R`` channel and ``R-base=4.0`` from ``conda-forge``.
  Moreover channels like ``conda-forge`` could have more updates than the default
  one, and could be difficult install or updating packages in those channels. Instead
  of installing our your requirements in a single environment, you should install
  software in dedicated environments, and use custom channels only if its necessary.

Export a conda environment
~~~~~~~~~~~~~~~~~~~~~~~~~~

You could export conda environment in a file. First, you have to activate the environment
that you want to import, for example::

  $ conda activate R-4.3
  $ conda env export > R-4.3.yml

.. hint::

  When you export an environment with conda, yon don't simply export infomations
  to re-build your environment relying on package version, but you also track information
  about the **package build version**, in order to be able to download the same file
  required to install a particular library.
  Sometimes is difficult to be able to re-create an exported environment, for example
  if you use packages in ``conda-forge`` channel: packages could be updated very
  often and maybe it is not possible to retrieve the same package file you used
  during environment import. For such cases, its better to export a conda
  environment without **build specifications**, like this::

    $ conda env export --no-builds > R-4.3.yml

  This will track all your package version without the file hash stored in conda
  channels. This require more time when restoring an environment, however you will
  be able to restore an environment after years even if you require some non-standard
  channels

Import a conda environment
~~~~~~~~~~~~~~~~~~~~~~~~~~

You could create a new environment relying on the exported file, for example on
a different machine::

  $ conda env create -f R-4.3.yml

Conda-pack
~~~~~~~~~~

Conda-pack is a tool which allows you to pack a conda environment in a single
file. This file can be moved to a different machine and unpacked in a different
location. This is useful when you want to move a conda environment to a different
machine without internet connection. You can install conda-pack with::

  $ conda install conda-pack

Then you can pack an environment with::

  $ conda pack -n R-4.3 -o R-4.3.tar.gz

.. hint::

  ``conda-pack`` is already installed in our shared **core** environment using
  the default ``base`` conda environment

.. warning::

  ``conda-pack`` will made a copy of all dependencies of your environment, thus
  the resulting file could be very large. You will make not use of conda packages
  caches, consider to use ``conda-pack`` only when is impossible to make an
  environment using the standard conda commands.

You can unpack the environment in a different location with::

  $ mkdir R-4.3
  $ cd R-4.3
  $ tar -xzf ../R-4.3.tar.gz
  $ source bin/activate

.. hint::

  If you unpack the environment in the conda environment folder (ie. ``$HOME.conda/envs``),
  you can activate the environment without specifying the full path (using the
  standard *conda activate* command, like ``conda activate R-4.3``), since conda
  will search for environments in the default location. Remember that you have to
  create the destination path, since the archive will not create it for you.

Remove an environment
~~~~~~~~~~~~~~~~~~~~~

You can remove an environment by specifying its *name*: this environment shouldn't
be active when removing::

  $ conda env remove --name R-4.3

Conda best practices
--------------------

Specify package version if possible
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Specifying package version could save a lot of time, for example when you need
to resolve dependencies with channels::

  $ conda create --channel conda-forge --channel R --name R-4.3 r-base=4.3

Clean up
~~~~~~~~

Conda will download and save packages in a local cache when installing or updating packages.
You can save some time when you install a cached package, however this can consume
a lot of disk space. You can free conda cache with::

  $ conda clean --all

See `conda clean <https://docs.conda.io/projects/conda/en/latest/commands/clean.html>`__
for more options.

Setting environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _conda_environment_variables:

In order to define specific environment variables in a conda environment, you
can use the `config API <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#setting-environment-variables>`__
or create specific `environment files <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#setting-environment-variables>`__
where variables are changed and restored respectively by activating and deactivating
the conda environment. The *config API* is the recommended and the easiest way
to define environment variables. In this example we will add a specific *JAVA library*
path to ``LD_LIBRARY_PATH``: first locate the directory with the *shared library*
to include, then call ``conda env config vars set`` to define and store the environment
variable. For the *JAVA* version we want to include, this library is located in
``$(JAVA_HOME)/lib/server``, where ``JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64``,
so::

  $ cd /usr/lib/jvm/java-11-openjdk-amd64/lib/server
  $ conda env config vars set LD_LIBRARY_PATH=$PWD:$LD_LIBRARY_PATH

After doing this, the conda environment should be *reactivated* (you could deactivate and
reactivate the same environment again) in order to get effects. You can inspect
the new environment variable by calling ``echo <variable name>``, for example::

  $ echo $LD_LIBRARY_PATH

or get the full list of custom variables using::

  $ conda env config vars list

Remember that when defining environment variables as collection of paths, the desired
path should be *prepended* to current paths, in order to retrieve the desired files
before the other positions. The current path should be updated and not replaced since it
could contains useful information.

.. warning::

  It's a bad idea to set the ``$PATH`` environment variable using the *config API*,
  since when disabling the conda environment, the ``$PATH`` will be unset, causing
  your terminal not working correctly. If you need to add a path to ``$PATH``, you
  need to manually edit the ``env_vars.sh`` files. Ensure to activate your desidered
  environment (in order to resolve the ``$CONDA_PREFIX`` environment variable) and
  then:

  .. code-block:: bash

    cd $CONDA_PREFIX
    mkdir -p ./etc/conda/activate.d
    mkdir -p ./etc/conda/deactivate.d
    touch ./etc/conda/activate.d/env_vars.sh
    touch ./etc/conda/deactivate.d/env_vars.sh

  Next, edit the ``./etc/conda/activate.d/env_vars.sh`` file and modify the ``$PATH``
  variable, for example:

  .. code-block:: bash

    #!/bin/sh

    export PATH="/home/core/software/sratoolkit/bin:$PATH"

  If you desire, you can restore the previous ``$PATH`` value by editing the
  ``./etc/conda/deactivate.d/env_vars.sh`` file:

  .. code-block:: bash

    #!/bin/sh

    # remove a particular directory from $PATH (define a new $PATH without it)
    # see: https://unix.stackexchange.com/a/496050
    export PATH=$(echo $PATH | tr ":" "\n" | grep -v '/home/core/software/sratoolkit/bin' | xargs | tr ' ' ':')

  See conda `Manaing environments <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#macos-and-linux>`__
  for more information.
