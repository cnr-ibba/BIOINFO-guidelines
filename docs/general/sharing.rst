
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

For more information see `Mastering user groups on Linux <https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html>`__

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
with the secondary group. To return to the original behaviour, you need to exit
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
-------------------

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
`Linux permissions: SUID, SGID, and sticky bit <https://www.redhat.com/sysadmin/suid-sgid-sticky-bit>`__
