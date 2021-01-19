Contributing
============

.. contents:: Table of Contents

Contributing to BIOINFO-guidelines
----------------------------------

This project is an attempt to write down some guidelines which need to be adopted
by people who works in our institution `IBBA-CNR <https://ibba.cnr.it>`__. Despite
this, the principles of those guidelines could be applied by everyone, so contributions
are welcome. Before starting to work on documentation, please follow those steps:

1. Open a `new issue <https://github.com/cnr-ibba/BIOINFO-guidelines/issues>`__
   in BIOINFO-guidelines repository, and get in touch with the developer: it's better
   to declare own intentions and talk with people before working on something that
   will never be included in such repository
2. Clone this repository in your GitHub account and clone it locally or where you
   plan to work on such documentation
3. Start a new branch, preferably as indicating the issue number of the issue you have
   opened on original repository (for example, if you created the issue #3, you should
   use ``issue-3`` as branch name)
4. Install ``sphinx`` requirements by using::

      pip install -r requirements.txt

   Inside project directory. You could install them in a conda environment, if you prefer
5. Work on documentation by adding or modifying files according your need. Track
   your modifications using git
6. Before submitting modifications, please check that documentation compiles
   without errors and warnings by::

     $ chdir docs
     $ make clean
     $ make docs

7. If documentation works as you intended, you could push your work on your GitHub
   account and start *from here* a new pull request
8. Give some times to the maintainers to review your code. If they requests you some
   modifications, please work in the same branch you used before and send your revision
   by committing in the same branch. Don't create a new branch or a new pull request,
   if you push your modifications on the same branch with a open pull request it will
   be updated accordingly

Thank you for your interest in BIOINFO-guidelines!
