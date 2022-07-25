
R environment
=============

.. contents:: Table of Contents

This document will illustrate a series of best practices to deal with a
R environment on our shared infrastructure. Ideally, ``R`` provide beautiful
*IDEs* like *RStudio*, however IDEs like that are not installed in our remote
infrastructure, since there are no *X* session available to display softwares with
a graphical interface. Despite this, the recommended way to work with our infrastructure
is to develop your analyses locally and then move your code and data to the
remote resources when you need to scale up
the calculations. Since the environment you have locally is different from the
remote environment, you have to operate in order to have minimal differences between
your environments: you have to track all your required dependencies, refer
to file locations relative to your project or script (without using *absolute paths*
which are not available remotely). Moreover the code executed remote and locally
should be the same, since you don't have to adapt your remote script
every time you need to change or fix your local script. All changes
in your code behavior between local and remote environments should be supplied
using *CLI* or parameter files (the last are preferred, since your analysis will be
*reproducible*).

R local installation
--------------------

``R`` is not installed by default in our system.
If you plan to use ``R`` with your analyses, you have to install ``R`` locally
in your ``$HOME`` directory, since this directory is mounted in the same position
in every instance in our infrastructure, an this means that you could install ``R``
in your *login* environment and then execute all your computational intensive
task in the *worker* nodes without worrying about synchronizing your libraries and
code between our machines. Here we describe two different approach to manage an
``R`` installation, using :ref:`conda <R-conda>` and :ref:`singularity <R-singularity>`
respectively.

.. _R-conda:

Create an R environment with conda
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Despite you can install R from *source*, the recommended (and easiest!) way to
install ``R`` is by using conda. Conda provides several ``R`` package and a dedicated
`channel <https://anaconda.org/r/repo>`__ in which ``R`` dependencies are
resolved. The ``r-essentals`` conda package  provide a lot of packages installed
with the ``r-base`` version, which provides the minimal software to run an ``R``
session. However if you plan to manage packages installation by yourself,
if you require the most update packages or your packages need to be installed from
source since they are not included in conda repositories, the most effective way is to
install the less from conda and then install your required packages from sources.
To create a new environment in ``conda`` with the latest version (*4.2.0* at the
moment) you can do like this::

  conda create --channel R --name R-4.2 r-base=4.2

.. hint::

  If you requires different R version, you should search also in ``conda-forge``
  channel, please see our documentation section :ref:`on channels <a-note-on-channels>`.

Even if we provide a lot of compilation libraries within our server instances, it
is possible that you can't compile and install an ``R`` package since there could be
missing dependencies. Conda provide several packages for compiling libraries, and
``R`` is enough smart to describe which library is missing. Using this information
you sould use ``conda search <package>`` and ``conda install <package>`` to resolve
the missing dependency. Sometimes, the package is already installed within the system
but you require to export an environment variable to install and use a particular
``R`` package. For example, for the ``rJava`` package we need to export a *JAVA*
environment variable to install such package, as described in the section
:ref:`Setting environment variables <conda_environment_variables>` in our documentation.
Briefly, in such case we need to add an environment variable to conda environment
using ``conda env config vars set``::

  $ JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
  $ conda env config vars set LD_LIBRARY_PATH="$JAVA_HOME/lib/server":$LD_LIBRARY_PATH

After that, you need to restart your conda environment (simply exit and re-enter
in your ``R`` conda environment) to take effects: you will be able to complete the
installation process and to use this compiled library within your projects.

.. _R-singularity:

Build an R environment through singularity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sometimes it's difficult to solve all your dependencies with conda or it could
be complex to figure out all the environment variables and the libraries paths
required to install your R packages properly. In these cases, it could be better
to manage the R version using :doc:`singularity <../general/singularity>`.
In the following example, we try to install all the dependencies required to
compile and install packages like ``rgdal``, ``terra`` and ``raster`` through
a singularity build which start from a ``R`` base image coming from docker and
in which we add some dependencies require to compile these packages. Here
is how it looks like the singularity ``.def`` file::

  Bootstrap: docker
  From: rocker/r-base:4.2.0
  Stage: build

  %post
      apt-get update && apt-get install -y \
          libgdal-dev \
          libudunits2-dev \
          libfontconfig1-dev \
          libharfbuzz-dev \
          libfribidi-dev
      NOW=`date`
      echo "export NOW=\"${NOW}\"" >> $SINGULARITY_ENVIRONMENT

  %runscript
      echo "Container was created $NOW"
      echo "Arguments received: $*"
      exec "$@"

Next, you will require an account to `Sylabs <https://cloud.sylabs.io/>`__,
since as a normal user you can't build locally a singularity image, you have to
do it *remotely* using a *singularity build service*. *Sylab* can provide you
time and space to build up images using a free tier. Once you have created an
account, login through your terminal with::

  $ singularity remote login

After that, you can build your custom images. Supposing that you have created a
definition file like before, and you named it ``rgdal.def``, you can build your
image using the ``--remote`` parameter::

  $ singularity build --remote rgdal.sif rgdal.def

Your singularity built image will be the ``rgdal.sif`` file. After that, you can
load the ``R`` just built with your all your defined dependencies with::

  singularity run rgdal.sif R

this will start an ``R`` terminal using your singularity image. Please see our
section on :doc:`singularity <../general/singularity>` to get more information. Please see also
`singularity documentation <https://docs.sylabs.io/guides/3.7/user-guide/>`__
to understand how create a *definition* file and which commands and parameters
are supported when calling ``singularity``

Manage dependencies with Renv
-----------------------------

Instead of installing your ``R`` packages *globally*, you can use
`Renv <https://RStudio.github.io/renv/articles/renv.html>`__ to manage the
transition between your local environment and the remote environment, and ensure
reproducibility between your projects. Briefly, ``renv`` install your dependencies
within projects, and this means that you could work with projects which have different
dependencies in the same time. Moreover this could help you when resuming a project
started long time ago, working with the same library versions you used when you
have started such project, without breaking your code since you have installed
a more recent version of such packages *globally*. Unlike
`packrat <https://RStudio.github.io/packrat/>`__, which build and install packages
inside your project folder, ``renv`` build packages once and links such packages
to the proper built directory when needed: this means that if you use the same
package between different projects, your package caches is built *once* and used
every time is needed, saving your time when re-using the same dependency across
your projects.

Your package dependencies will be tracked using the ``renv.lock`` file, which is created
and managed through ``renv`` command. There will also an ``renv`` folder
in which some filer required by ``R`` to find and load your packages correctly
are located. Simply manage your packages as usual, and then call::

  > renv::snapshot()

To save the state of your libraries to the ``renv.lock`` file. Once you are ready
to move your code on remote environment, remember to synchronize your ``renv.lock``
file. After that, you can use::

  > renv::restore()

to install your required libraries on your remote environment, without installing
your libraries one-by-one after test for their presence on the remote environment.

.. hint::

  Sometimes it could be impossible to restore all your dependencies from
  the ``renv.lock`` file: ``renv`` developers can't
  ensure you that such process will be successful every time. If you have trouble when
  restoring an environment, you can call ``renv::purge()`` by providing the package
  name which gave you issues, in order to clean up the problematic package. Sometimes
  you require to restart your R session, to see changes in your working environment.
  Tracking ``renv.lock`` with your code using ``git`` (or backing up your ``renv.lock``
  file) is   *strongly recommended*. There can be also cases in which you have
  to clean up your environment, please refer to
  `renv documentation <https://RStudio.github.io/renv/reference/index.html>`__.

The here package
----------------

You have to avoid to refer to your scripts or data files using *absolute paths*,
since the paths you have in your local R installation are different from the path
you will find on remote environment. Using a package like
"`here <https://here.r-lib.org/>`__" can help you to code your paths relying on
``R`` environment. The ``here()`` function (which has the same name of the package)
return the absolute location of your ``R`` project file, and by providing the
*relative path* of a file respect to your project as an argument you receive an
*absolute path* as a return value, which can be used to deal with file locations
in different OS (like windows and linux, for instance) and with different project
locations. For example::

  > here("directory", "file")

will return the absolute path of ``directory/file`` file relative to your ``.Rproj``
file location.

.. important::

  In order to use the ``here`` package you have to define a ``.Rproj``
  file at the top of your project. Creating the project using *RStudio* is the
  recommended way for doing that.

Calling rmarkdown from terminal
-------------------------------

Since ``RStudio`` is not available on our remote infrastructure, you cannot render
a ``.Rmd`` file by clicking on *knitr* button on like on your *RStudio* IDE. However, you
are able to call ``rmarkdown::render()`` and provide the location of your ``.Rmd``
script as parameter. For example, if you define a ``.Rmd`` file like this::

  ---
  title: "A sample Rmarkdown file"
  author: "Paolo Cozzi"
  date: "`r Sys.Date()`"
  output: html_document
  params:
    param1: "a simple string param"
    param2: 42
  ---

  ```{r setup, include=FALSE}
  knitr::opts_chunk$set(echo = TRUE)
  ...

Than you can render this file using ``rmarkdown::render(<your script>)``, or better
by defing a new script which call your ``.Rmd`` file, for example by loading your
libraries using ``renv`` and finding your paths using ``here`` packages::

  #! /usr/bin/env -S Rscript --slave --vanilla

  # activate environment
  library(renv)
  renv::activate()

  library(rmarkdown)

  rmarkdown::render(
    "<your script>",
    params = list(
      param1 = "override param1 in your .Rmd file",
      param2 = 101
    )
  )

In this example, we define some parameters in our ``.Rmd`` script, and then we override
them using the ``Rscript`` file. We could also make use of the ``optparse`` package
in order to accept parameters from CLI, and them provide them when calling the render script.
This bring us some advantages: we can define our ``.Rmd`` file to render some test
data by default in our local environment by calling *knitr* directly in our Rstudio
session. Then on our remote infrastructure we can provide the real data from *command
line* without modifying our script.

.. hint::

  If you are used to save cache with markdown, maybe you have to clean up your
  project in your ``Rscript``.

R best practices
----------------

At the end of this document, we can try to sum up some behaviors that should be
adopted when porting your local projects on a remote shared infrastructure without
``RStudio``:

- Manage your ``R`` projects using ``git`` is *strongly recommended*.
- Don't use *absolute* paths: use *relative* paths when possible, or manage
  your file locations using R packages like ``here``.
- When using ``rmarkdown``, customize your analysis using parameters.
- Create a very simple ``R`` script, in which you will render your ``.Rmd`` file.
- Never create a big script, instead split your code in steps and save a ``RDS``
  data file after each step completion.
- If you require the *RStudio* to manage your plots, copy your ``RDS`` data locally
  and then work in your preferred environment.
- Don't call a CPU intensive calculation without ensuring all your dependencies are
  installed correctly and without testing your analysis workflow with a small set
  of data, which can return results *immediately* or *very quickly*.
- Track your dependencies in a file, better if you manage them with ``renv``.
- If you use ``renv`` to manage dependencies between your environments, remember
  to save your environment every time you add/remove a dependencies and to
  synchronize your local environment with your remote environment.
