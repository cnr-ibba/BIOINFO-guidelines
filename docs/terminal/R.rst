
R environment
=============

This documentation will illustrate a series of best practices to deal with a
R environment on our shared infrastructure.

R local installation
--------------------

If you plan to use ``R`` with your analyses, you should try install ``R`` locally
in your ``$HOME`` directory, since this directory is mounted in the same position
in every instance in our infrastructure, an this means that you could install ``R``
in your *login* environment and then execute all your computational intensive
task in the *worker* nodes without worrying about synchronize your libraries and
code between our machines.

Create an R environment with conda
----------------------------------

Despite you can install R from *source*, the recommended (and easiest!) way to
install ``R`` is by using conda. Conda provides several ``R`` package and a dedicated
`channel <https://anaconda.org/r/repo>`__ in which ``R`` dependencies are
resolved. The ``r-essentals`` conda package  provide a lot of packages installed
with the ``r-base`` version, however if you plan to manage packages by yourself,
if you require the most update packages or your packages need to be installed from
source since is not included in conda repositories, the most effective way is to
install the less from conda and then install your required packages from sources.
To create a new environment with the latest version (*4.2.0* at the moment) you can
do like this::

  conda create --channel R --name R-4.2 r-base=4.2

.. hint::

  If you requires different R version, you should search also in ``conda-forge``
  channel, please see our documentation seaction :ref:`on channels <a-note-on-channels>`

Even if we provide a lot of compilation libraries within our server instances, it
is possible that you can't compile and install an ``R`` packages since there are
missing dependencies. Conda provide several packages for compiling libraries, an
``R`` is enough smart to describe which library is missing. Using this information
you sould use ``conda search <package>`` and ``conda install <package>`` to resolve
the missing dependencies. Sometimes, the package is already installed within the system
but you require to export an environment variable to install and use a particular
``R`` package. For example, for the ``rJava`` package we need to export a *JAVA*
environment variable to install such package, as described in the section
:ref:`Setting environment variables <conda_environment_variables>` in our documentation.
Briefly, in such case we need to add an environment variable to conda environment
using ``conda env config vars set``::

  $ JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
  $ conda env config vars set LD_LIBRARY_PATH="$JAVA_HOME/lib/server":$LD_LIBRARY_PATH

After that, you need to restart your conda environment (simply exit and re-enter
in your ``R`` conda environment) to take effects.

Build an R environment through singularity
------------------------------------------

Sometimes, it's difficult to solve all your dependencies with conda or it could
be complex to figure out all the environment variables and the libraries paths
required to install your R libraries properly. In those case, it could be better
to manage the R version using :doc:`singularity <../general/singularity>`.
In the following examples, we try to install all the dependencies required to
compile and install libraries like ``rgdal``, ``terra`` and ``raster`` through
a singularity build which start from a ``R`` base image coming from docker. Here
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
definition file like before, and you named it ``rgdal.def``, build you image using
the ``--remote`` parameter::

  $ singularity build --remote rgdal.sif rgdal.def

Your singularity build images will be the ``rgdal.sif`` file. After that, you can
load the ``R`` just built with your all your defined dependencies with::

  singularity run rgdal.sif R

This will start an ``R`` terminal using your singularity image. Please see our
section on singularity to get more information. Please see also
`singularity documentation <https://docs.sylabs.io/guides/3.7/user-guide/>`__.

Manage dependencies with Renv
-----------------------------

Call rmarkdown from terminal
----------------------------

R best practices
----------------

- When using ``rmarkdown``, customize your analysis using parameters
- Create a very simple ``R`` script, in which you will render your ``.Rmd`` file.
- Never create a big script, instead divide your code in steps and save a ``RDS``
  data file after each step completion
- If you require the *rstudio* to manage your plots, copy your ``RDS`` data locally
  and then work in your preferred environment.
- Don't call a CPU intensive calculations without ensuring all your dependencies
  installed correctly and without testing your analysis workflow with a small set
  of data, which can return results *immediately* or *very quickly*
