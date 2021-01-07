
Conda
=====

.. contents:: Table of Contents

About Conda
-----------

Conda is a software which allows you to manage software installations in distinct
environments. It was born to support the python ecosistem, however most softwares
has been supported by conda, for example `R`_ and its packages, and there are
channels like `bioconda`_, which collect and maintain a lot of useful softwares.
The main advantage in using conda environments is that packages could be installed
directly with their dependencies, whitout the needing to compile everithing. Moreover
conda and its environments can be installed by an user without administrative privileges.
Packages and dependencies are installed inside user directories, and a complete
unistallation could be done by erasing the conda installation folder.
From the `conda`_ official documentation:

.. _R: https://docs.anaconda.com/anaconda/user-guide/tasks/using-r-language/
.. _conda:  https://docs.conda.io/en/latest/index.html
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

Should I install Conda or Miniconda?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Conda is installed with a a lot of dependencies, like spyder editor, jupyter notebook
and many other packages. Miniconda is a lighter version of anaconda, which installs
only the minimal packages required to work correctly with conda. In general, you could
decide to install the whole Conda in a local installations, since locally you could
exploit the benefit of the editors and the grafical user interfaces in general.
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
