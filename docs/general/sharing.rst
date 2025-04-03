
Shared folders and permissions
==============================

.. contents:: Table of Contents

Primary and secondary groups
----------------------------

User groups let people share files with other users and let sysadmin to manage
permissions on a group of users, rather than individual users. When a user is added
on a linux system, usually receives a primary group which is the same of the username.
Then the sysadmin could add such user in a secondary group, in order to share files
with others users. Primary and secondary groups are intended to separate personal
files from files that could be shared with other people. You can have information
on your groups by calling ``id`` or ``groups`` command with your username::

  $ groups cozzip
  cozzip : cozzip core
  $ id # username is optional to get own information with id
  uid=1000(cozzip) gid=1000(cozzip) groups=1000(cozzip),1004(core)

Usually your primary group is the first group you get with ``groups`` commands,
while the others are secondary group. You can have the same information by inspecting
``/etc/passwd`` and ``/etc/group`` respectively for primary and secondary groups::

  $ grep cozzip /etc/passwd
  cozzip:x:1000:1000:Paolo Cozzi:/home/cozzip:/bin/bash
  $ grep cozzip /etc/group
  cozzip:x:1000:
  core:x:1004:cozzip

For more information see `Mastering user groups on Linux <https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html>`_

Log in into a new group
-----------------------

When you log in a linux system, you will login with your user id (``uid``) and you
primary group (``gid``). Every file or directory you create will have such
``uid`` and ``gid`` ownership::

  $ id cozzip
  uid=1000(cozzip) gid=1000(cozzip) groups=1000(cozzip),1004(core)
  $ touch test
  $ ls -l test
  -rw-rw-r-- 1 cozzip cozzip 0 Jan 19 12:06 test

You can change temporarily your ``gid`` by calling a secondary group which you belong
to with ``newgrp`` command. After that, every file or directory you create will have
the selected secondary groups as owner::

  $ newgrp core
  $ touch test_group
  $ ls -l test_group
  -rw-rw-r-- 1 cozzip core 0 Jan 19 12:51 test_group

When you change the secondary group with ``newgrp``, you will make a new login
with the secondary group. To return to the original behavior, you need to exit
from the group login with ``exit`` command

.. hint::

  if you copy files using ``rsync`` you can use ``--chown=<user>:<group>`` option
  to set destination ownership. For more information, please see :ref:`rsync section <copy-files-with-rsync>`
  in :ref:`Copying files using OpenSSH <copying-files-using-openssh>`.

.. warning::

  ``newgrp`` is valid only on your current shell, every new  login will have the
  default primary group set. You could start new login from a ``newgrp`` shell,
  for example with ``tmux``: every tmux shell you will open will have the selected
  secondary groups since they are child processes of your first ``newgrp`` shell

.. danger::

  the ``newgrp`` will start a new login shell, so all you *bash* initialization
  scripts are executed again. **Never put a ``newgrp`` commands inside your bash initialization
  scripts** or you will execute your initialization scripts forever (or finally
  your login process will be killed from the system).

Setting permissions
-------------------

Set group ownership
~~~~~~~~~~~~~~~~~~~

You could change group ownership of a file or a directory with ``chgrp`` and
your group name (you should belong to it to change group ownership)::

  $ mkdir test_dir
  $ chgrp core test_dir/

The ``-R`` or ``--recursive`` change ownership for the specified folder and all
its content

.. tip::

  In our **core** environment, we have the ``/usr/local/bin/fix_permissions.sh``
  scripts which fix permission for groups recursively. First, move into directory
  that you would like to share, then call ``fix_permissions.sh`` by passing the
  desidered group::

    $ cd /folder/to/share
    $ fix_permissions.sh core

Using SGID
~~~~~~~~~~

The ``sgid`` (or group + special) is a special permission which have two mainly
function:

* on a file, it allows execution as the group that own the file
* on a directory, every file or directory created in such directory will have the
  same group as the parent folder

By setting the ``sgid`` on a folder, you will not need to fix file ownership on
directory content nor login with ``newgrp`` using such group. You will need only
to set this type of permission on the top level folder and ensure that the group
ownership is correct, for example::

  $ mkdir test_dir
  $ chgrp core test_dir/
  $ chmod g+s test_dir/
  $ ls -ld test_dir/
  drwxrwsr-x 2 cozzip core 10 Jan 19 13:54 test_dir/

The ``s`` letter on the group triplet permission means that ``sgid`` is correctly
set. You could do the same thing by setting ``2775`` octal code (the ``2`` before
the standard ``775`` is the ``sgid`` octal code)

.. hint::

  In our **core** environment, he have the ``/home/core`` folder with the ``sgid``
  set for the ``core`` group. All files that need to be shared with ``core`` members
  need to be placed inside this folder.

.. warning::

  Despite ``sgid`` keeps the same permission of parent folder when creating new
  files or directory, it can't set permission when moving files from one location
  or another or when unpacking data from archives. **When moving files accross directories
  or when extracting files from archives, please check that permissions are correct.**
  If you transfer files using ``rsync``, you could set ``sgid`` in source folder
  and transfer attributes with ``-a`` option. See :ref:`rsync section <copy-files-with-rsync>`
  for more information.

For more information on special permission, see
`Linux permissions: SUID, SGID, and sticky bit <https://www.redhat.com/sysadmin/suid-sgid-sticky-bit>`_

Working with umask
------------------

Understanding umask
~~~~~~~~~~~~~~~~~~~

The `umask` (user file-creation mode mask) is a Linux command and configuration
setting that determines the default permissions for newly created files and
directories. It essentially "masks" certain permission bits, ensuring that files
and directories are not created with overly permissive access.

Default Permissions
~~~~~~~~~~~~~~~~~~~~

When a file or directory is created, it starts with a default set of permissions:

- Files: `666` (read and write for everyone, no execute)
- Directories: `777` (read, write, and execute for everyone)

The `umask` subtracts permissions from these defaults. For example, if the `umask`
is `002`, the resulting permissions for a file will be `664` (read and write for
owner and group, read-only for others), and for a directory, it will be `775`
(read, write, and execute for owner and group, read and execute for others).

Checking and Setting umask
~~~~~~~~~~~~~~~~~~~~~~~~~~

You can check the current `umask` value by running:

.. code-block:: bash

  $ umask
  002

To temporarily set a new `umask` value, use the `umask` command followed by the
desired value:

.. code-block:: bash

  umask 007

This will set the `umask` to `007`, ensuring that new files and directories are
not accessible by others outside the group.

To make the change permanent, add the `umask` command to your shell's initialization
file (e.g., `.bashrc` or `.zshrc`).

Using umask for Group Collaboration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To allow members of the same group to work on shared files, you should:

1. Ensure all users are part of the same group.
2. Set the `umask` to `002` for all users in the group. This ensures that new
   files and directories are created with group write permissions.
3. Use the `sgid` bit on shared directories to maintain group ownership
   (as explained in the previous section).

Example Workflow
~~~~~~~~~~~~~~~~

1. Create a shared directory and set the group ownership:

  .. code-block:: bash

    mkdir shared_folder
    chgrp core shared_folder
    chmod g+s shared_folder

1. Set your `umask` to `002` to let all users in the group to modify files

  .. code-block:: bash

    umask 002

3. Verify that new files and directories inherit the correct permissions:

  .. code-block:: bash

    $ cd shared_folder
    $ touch test_file
    $ mkdir test_dir
    $ ls -l
    -rw-rw-r-- 1 user core 0 Jan 19 14:00 test_file
    drwxrwsr-x 2 user core 6 Jan 19 14:00 test_dir

.. warning::

  If a user's `umask` is not set to `002`, their files may not have group write
  permissions, disrupting collaboration. Ensure all users in the group configure
  their `umask` correctly.

For more information on `umask`, see `Understanding umask
<https://linuxize.com/post/umask-command-in-linux/>`_.
