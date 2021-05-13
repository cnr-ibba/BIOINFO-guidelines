
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

Generate a public key pair under ssh
""""""""""""""""""""""""""""""""""""

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
administrator in order to be able to connect remotely.
