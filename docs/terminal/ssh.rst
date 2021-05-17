
SSH
===

.. contents:: Table of Contents

About SSH
---------

.. epigraph::

  The Secure Shell Protocol (SSH) is a cryptographic network protocol for operating
  network services securely over an unsecured network. Typical applications
  include remote command-line, login, and remote command execution,
  but any network service can be secured with SSH

  -- https://en.wikipedia.org/wiki/Secure_Shell_Protocol

**SSH** is the preferred method we use to work on remote server: it can be used
to connect and to open a new terminal, retrieve files and executing commands. In
order to be able to work on a remote server with SSH, there are some conditions to
be satisfied:

1. A SSH **server** instance need to be up and running on a **remote machine**
2. You are **allowed** to connect on a **remote machine**
3. You need a SSH **client** on your **local machine** to be able to start a connection

While the first two points are a matter of the remote server system administrator,
you require to install a configure a client in order to work on remote server.

SSH Authentication
------------------

There are many to authenticate yourself to a remote server using SSH protocol,
however the most common are:

- **Password authentication**: Client will ask you to enter a password,
  will encrypt it and use it to authenticate itself to a server.
- **Public key authentication**: Each client uses a key pair to authenticate
  itself to a server. There will be a **private** key which identity the user
  and a **public key** which need to be present remotely on the server.
  To allow connections from client, server should find the
  corresponding public key in the list of allowed keys and, more important,
  **the pairing between keys** need to be preserved: you can't connect using a
  private key of a different pair of keys.

.. warning::

  In our infrastructure we have disabled the **password authentication**, only the
  **public key authentication** is allowed

For more information see `6 ssh authentication methods <https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/>`__.

SSH Clients
-----------

There are a lot of SSH clients, which depends on the operating system you have and
your personal preferences. Despite they are different in configuration and usage, they
act in the same way while working with remote servers.

.. hint::

  Despite the number of different way to provide remote access using SSH protocol,
  the :ref:`OpenSSH <openssh-client>` method is the preferred and most recommended way to connect to
  our infrastructure

.. warning::

  In our shared environment we provide SSH access only via **public key authentication**.
  You need to understand how your ssh client works and how to generate a **private/public
  keys pair** order to connect to our remote machines. **Only the public key** need to
  be forwarded to the system administrator in order to grant you access on shared
  remote machines. Your private key need to be stored securely and **never** be sent
  to anyone in order to enforce security and prevent to others to access to your
  resources using your credentials. Please see ssh documentation regarding
  `public key authentication <https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Public_Key_Authentication>`__

OpenSSH
~~~~~~~

.. _openssh-client:

OpenSSH is the *de facto* standard for remote login with SSH protocol in Linux/MacOS
environments. Beyond login, it provides file transfer with ``scp`` and ``sftp``, it
manages keys with ``ssh-keygen`` and ``ssh-copy-id`` and provide more advanced functionalities
with ``ssh-agent``. More info on OpenSSH could be found `here <https://www.openssh.com/>`__.
To discover if you have the OpenSSH client installed on your local (Unix) machine,
simply type::

  $ ssh

On your terminal. In case you don't have OpenSSH installed, you could install the
``openssh-client`` package (there's also a ``openssh-server`` but is required only
if you want to provide remote connections on your local machine)

Generate a public key pair with OpenSSH
"""""""""""""""""""""""""""""""""""""""

The easiest way to generate a key pairs using ssh is by using ``ssh-keygen``. This
util request to to provide the path where to store the key pair and a passphrase
required when using your key pairs. You could reply with no arguments (simply press
``enter`` key) to leave the default options::

  $ ssh-keygen
  Generating public/private rsa key pair.
  Enter file in which to save the key (<your home>/.ssh/id_rsa):
  Enter passphrase (empty for no passphrase):
  Your identification has been saved in <your home>/.ssh/id_rsa
  Your public key has been saved in <your home>/.ssh/id_rsa.pub

In case you have already generated a key pair with the same file name, you are
prompted if you want to overwrite your key pair::

  <your home>/.ssh/id_rsa already exists.
  Overwrite (y/n)?

.. danger::

  Please, be careful before generating a new key pair: if you overwrite an existent
  key, you will not be able to connect remotely to other machines configured with
  the old key pair

Please keep track of your public key (which is the one with the ``.pub`` extension
the ``id_rsa.pub`` file). If you used the default options, such file is stored in your
``$HOME/.ssh/`` folder): This is the file you need to provide to your system
administrator in order to be able to connect remotely. After that, please see
:ref:`OpenSSH <openssh-connect>` section under `Remote connection to a Server`_
section.

Remote connection to a Server
-----------------------------

In order to connect to a remote server with a public key pair, you public key file
need to be placed inside your ``$HOME/.ssh/authorized_keys`` file on remote host::

  $ tree .ssh/
  .ssh/
  ├── authorized_keys
  └── known_hosts

Moreover, in order to connect, those files need to be accessed only by your user
(with the ``700`` and ``600`` ``chmod`` permission for directory and files
respectively)::

  $ ll -d .ssh/
  drwx------ 2 cozzip cozzip 100 May 12 12:42 .ssh/
  $ ll .ssh/authorized_keys
  -rw------- 1 cozzip cozzip 3.2K May  6 10:02 .ssh/authorized_keys

Those permission are **required** in order to allow remote connection. If not, you
can't use your public key for authentication. To copy your public key in the
remote ``$HOME/.ssh/authorized_keys`` files, you can copy your public key inside
the file content or use ``ssh-copy-id`` from your *local* terminal (only for OpenSSH
users)::

  $ ssh-copy-id -i $HOME/.ssh/id_rsa.pub <user>@<remote server>

Where the option ``-i`` define the path of your public key file. ``<user>`` and
``<remote server>`` are respectively your *username* in the remote machine and
the remote machine address (which could be an *ip address* like ``192.168.122.100``
or a *domain name*). This script will copy your public key in the ``authorized_keys``
and will check the correct permissions.

.. note::

  SSH access without public key is *disabled* in our infrastructure, so you can't copy
  a public key by yourself for the first time. This is why you have to provide
  the *public key* to the system administrator. After your access is granted,
  you can use ``ssh-copy-id`` to copy another *public key* (of another machine
  for example) using the enabled key pair, for example::

    $ ssh-copy-id -f -i /path/to/another/public_key.pub

  the ``-f`` option will force the copy of a public key without ensuring the existance
  of the proper identity file.

Connecting with OpenSSH
~~~~~~~~~~~~~~~~~~~~~~~

Start a new connection
""""""""""""""""""""""

.. _openssh-connect:

In order to remote-connect using OpenSSH (once your public key is properly set),
you will to call ``ssh`` command by specify your *remote username* and *remote machine*,
for example::

  $ ssh <user>@<remote server>

This will be sufficent to login, if you have your **private key** in the default
location (you haven't specified a different path for your key files during creation).
In case you don't have your private key in the default location (or you have chosen
a different name) you could provide your private key file with the ``-i`` identity
option::

  $ ssh -i /path/to/your/private/id_rsa <user>@<remote server>

.. hint::

  If you have choose a *passphrase* when creating your key pairs, you require to
  provide the same *passphrase* when connecting to a remote server with such key
  pair. A more pretty solution could be load your key in a *ssh-agent* and provide
  the passphrase once. The agent will provide your keys everytime needed without
  asking for passphrase. Simply type::

    $ ssh-add /path/to/your/private/id_rsa

  see `Passwordless Login <https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Public_Key_Authentication#Passwordless_Login>`__
  for more information

.. warning::

  If you are trying to connect to a remote server for the first time, you will
  receive a message like this::

    The authenticity of host 'xxxxxxxxxxxxxx (xxx.xxx.xxx.xxx)' can't be established.
    ECDSA key fingerprint is SHA256:cdjcdncjdsnckjnscjkndcjkdsckmdkcmdkcd.
    Are you sure you want to continue connecting (yes/no/[fingerprint])?

  Simply type ``yes`` when prompted and you will proceed with connection.
  The host/ip address of the remote server will be placed in your
  ``$HOME/.ssh/known_hosts`` file. This message will not be printed again when
  connecting to the same host.

.. danger::

  Every time you start new a connection to a remote server, ssh checks server
  fingerprint with the information stored in ``.ssh/known_hosts``. If the server
  fingerprint is different, the connection is immediately terminated. There could
  be different reasons when you see this behaviour, for example your administrator
  may have changed the destination server or maybe someone has hacked your server
  configuration or connection. When you see an issue like this, please tell immediately
  it to your system administrator.

Closing a connection
""""""""""""""""""""

To exit from the remote terminal and logount from the remote server, simply type::

  $ exit

In order to close the remote session.

OpenSSH connection options
""""""""""""""""""""""""""

OpenSSH let you to store connetion parameters in the ``$HOME/.ssh/config``
configuration file. There are options which are applied everytime you start a OpenSSH
connection with ``ssh`` or options that are applied only on specific remote server.
You could also choose to override global configuration by specifing the same parameters
in the specific remote section. The ``$HOME/.ssh/config`` could be structured like
this::

  # these settings are applied everytime you start a ssh connection
  ServerAliveInterval=60
  ServerAliveCountMax=20
  ConnectTimeout=60

  # The following settings are host specific. The pattern is valid for all the
  # 192.168.122.0/24 subnet (every server from 192.168.122.1 to 192.168.122.254)
  Host 192.168.122.*
    # these option will replace the default ones with new values
    ServerAliveInterval=30
    ServerAliveCountMax=10
    ConnectTimeout=30

    # you can provide a specific identity for such remote server
    IdentitiesOnly yes
    IdentityFile /path/to/your/private/id_rsa

The ``IdentityFile`` could be used to define your private key location, in order
to not provide your identity file everytime you start a new connection,
``ServerAliveInterval``, ``ServerAliveCountMax`` and ``ConnectTimeout`` are respectively
timers which regulate the timeouts when connecting and in sending messages between
client and servers. They could be useful when connecting using a unreliable network
For more information on ssh ``config`` and keys see
`Associating Keys Permanently with a Server <https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Public_Key_Authentication#Associating_Keys_Permanently_with_a_Server>`__,
while for more information on ssh client options see the `ssh manual pages <https://linux.die.net/man/1/ssh>`__

Copy remote files using SSH
---------------------------

Copying files using OpenSSH
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _copying-files-using-openssh:

There are two ways to copy a file using OpenSSH, they are ``scp`` and ``rsync``.
``scp`` is part of OpenSSH package while ``rsync`` is another utility to copy file
which support ``SSH`` protocol. Despite the two methods are valid, ``rsync`` is the
recommended way since it can do incremental copy (ie copy only new or updated files)
and can preserve file permissions and times (which are useful to understand if a
file is updated or not).

SCP
"""

``scp`` works like linux ``cp`` but support remote origin and destination. Simple
prefix your source or destination path with ``<user>@<remote server>`` as you do when
connecting using OpenSSH, for example to copy recursively from a remote folder in
your local environment::

  $ scp -r <user>@<remote server>:/remote/src/path /local/dst/path

If you want to copy a local folder into a remote folder, simply add the
``<user>@<remote server>`` before destination directory::

  $scp -r /local/src/path <user>@<remote server>:/remote/dst/path

.. note::

  remember to add a ``:`` between ``<user>@<remote server>`` and your remote folder,
  otherwise you will do a local copy with as ``<user>@<remote server>`` as prefix.
  ``<user>@<remote server>:`` without destination folder is a shortcut for your remote
  ``$HOME`` directory

.. warning::

  The main issue with ``scp`` is that you can't copy file attributes, for example
  timings: your copied file will have the created/modified time when the copy occurs,
  and you can't define the most updated file simply relying on date. Moreover, if you
  remote copy a folder using ``scp``, you will copy the whole directory content,
  indipendently if destination files are already present or aren't changed. This
  need to be taken into consideration for example if there are network issues during
  copying and you need to executing the same command again: for those reasons,
  ``rsync`` is the recommended way to copy or backup files using ``OpenSSH``.

Rsync
"""""

.. _copy-files-with-rsync:

``rsync`` is the recommended way to backup or copy files from/to remote services:
it checks contents in destination folder in order to save time and bandwith by copying
only new or modified files. Command is similar to scp, however there are additional
parameters that need to be mastered in order to take advantage of ``rsync``. For
example, to copy file from local to remote your could do like this::

  $ rsync -vare ssh /local/src/path <user>@<remote server>:/remote/dst/path

Here are the main options of ``rsync``:

- ``-v``: verbose transfer
- ``-a``: archive (track attributes like permissions)
- ``-r``: recursively
- ``-e ssh``: ``-e`` define the protocol used in transfer, need to be followed immediately
  by ``ssh``. You can specify parameters in different order, but when you set ``-e``
  parameter, you need to specify protocol.

There are other options that are useful and that can be added to ``rsync`` command
line:

- ``-P``: show progress during copying
- ``-u``: skip files that are newer on the receiver
- ``-n``: *dry-run* (useful in testing ``rsync`` commands)
- ``-z``: use *gzip* while transferring (useful with text files and slow connections)
- ``--del``: delete destination files if they don't exists on source (use with caution,
  test ``rsync`` command before apply)
- ``--exclude=pattern``: exclude *pattern* from ``rsync``
- ``--chown=<user>:<group>``: set destination ownership (``user`` and ``group`` need
  to exists in destination)

.. warning::

  With ``rsync`` a path like ``/local/source/folder/`` (with final ``/``) is
  a shortcut for all file contents in folder (ie ``/local/source/folder/*``),
  while omitting the final ``/`` mean the folder itself. So a command like::

    $ rsync -vare ssh /local/src/path1/ <user>@<remote server>:/remote/dst/path2/

  Will place all ``path1`` contents in ``path2`` contents, while::

    $ rsync -vare ssh /local/src/path1 <user>@<remote server>:/remote/dst/path2/

  Will place ``path1`` directory in ``path2`` (so, destination will be: ``/remote/dst/path2/path1``).
  The same applyes by avoiding the final ``\`` in destination path. Please,
  launch a *dry-run* rsync (with ``-n`` option) to ensure that your ``rsync`` command
  line is correct

Mount remote folders using SSH
------------------------------

It is possible to mount a remote folder in your local environment using ``sshfs``.
Briefly, this utility let you to mount a remote folder into your local environment
using ``SSH`` as protocol. This has the benefit that you could see the remote
files like as they are on your local environment. For example, by mounting a
folder like this you can edit your remote files with your preferred editor or
inspect remote file contents using your file browser.
In linux, you can mount remote folder by installing ``sshfs`` packages. For MacOS,
you need `oxfuse <https://osxfuse.github.io/>`__ packages. After installing required
packages, you need to create destination path in which mount remote folder. For
example::

  $ sudo mkdir /mnt/core

.. hint::

  If you create a directory outside your ``$HOME`` directory, you need to call
  command with ``sudo`` in order to create such folder. Next, ensure you own such
  directory in order to mount remote folder as a user with::

  $ sudo chown $USER:$USER /mnt/core

After that, you could mount the remote folder with::

  $ sshfs -o idmap=user <user>@<remote server>:<remote directory> /mnt/core/

``-o idmap=user`` is an option required in order to save/retrieve files with your
ssh credentials. If you need to unmount a folder::

  $ fusermount -u /mnt/core
  # Or if you are on a mach and you don't have ``fuse`` installed
  $ sudo umount /mnt/core

For more informations, see
`How To Use SSHFS to Mount Remote File Systems Over SSH <https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh>`__
