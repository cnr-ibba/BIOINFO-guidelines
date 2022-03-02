
Tmux
====

.. contents:: Table of Contents

About Tmux
----------

Tmux is a virtual terminal and is useful in a remote environment (like after connecting
with `ssh`) since lets you to call *commands* and *softwares* in one or more virtual 
*windows*, which will not be interrupted if the network goes down or you need to 
terminate your local terminal (ie you need to go home :-)). From the tmux man pages:

.. epigraph::

    tmux is a terminal multiplexer: it enables a number of terminals to be 
    created, accessed, and controlled from a single screen.  tmux may be 
    detached from a screen and continue running in the background, then later 
    reattached

Tmux is already installed in our *core* shared machine and should be started
**immediately** after connecting with SSH, on the remote environment: you could 
even install and start tmux in your local environment, but if you connect to the 
remote machine in a tmux local terminal, your tasks will be interrupted when 
network has issues or you local environment is turned off.

.. hint:: 

    The main idea with tmux is to start a session remotely, in order to detach
    and leave your process in background, even after logging out the remote 
    environment. All the information returned to the virtual terminal will be 
    available when reconnecting to the same tmux session and window, even by 
    using another connection or another device.

Tmux sessions
-------------

You should create a tmux session using different *names*, for example using the 
name of projects you are working on. This let you to define multiple sessions and to 
work at different projects at the same time, and let you to switch from one terminal
to another if your are waiting for your program to finish. Here is how to start 
a new tmux session::

    $ tmux new -s <session name>

where ``<session name>`` will be the name of your session. By creating a new session,
a new terminal inside a tmux window is started. To connect to an existent tmux 
session, first display your active and running session names with::

    $ tmux ls 

Then attach to a session by providing the session name with::

    $ tmux attach -t <session name>

Tmux control command
~~~~~~~~~~~~~~~~~~~~

After a tmux terminal is started, you will start a new terminal inside a tmux 
session and you can work in the same way you could work outside tmux. If you need 
to take control to the tmux terminal, you need a combination of a prefix key, which is
``C-b (Ctrl-b)`` by default, followed by a command key. For instance, to leave 
the current tmux session, leaving the terminal and its processes running in background
you need to press ``Ctrl-b`` and then the letter ``d`` (which stands for *detach*
from your current session). Other useful command keys are:

+----------------------+------------------------------------------------------------+
| Command              | Behaviour                                                  |
+======================+============================================================+
| ``Ctrl-b`` + ``d``   | *Detach* the current session                               |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``c``   | *Create* a new window                                      |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``w``   | *Move* across window using arrow keys                      |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + <num>   | *Switch* to the window *num*                               |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``%``   | *Split* window *vertically*                                |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``$``   | *Rename* session                                           |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``,``   | *Rename* window                                            |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``"``   | *Split* window *horizontally*                              |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + <space> | *Move* from *horizontal* split to *vertical* and viceversa |
+----------------------+------------------------------------------------------------+
| ``Ctrl-b`` + ``s``   | *Move* across sessions using arrow keys                    |
+----------------------+------------------------------------------------------------+

Here are some resources where you can find more informations and tips:

* `A tmux Crash Course <https://thoughtbot.com/blog/a-tmux-crash-course>`__
* `tmux & screen cheat-sheet <http://www.dayid.org/comp/tm.html>`__
* `Tactical tmux: The 10 Most Important Commands <https://danielmiessler.com/study/tmux/>`__
* `tmux(1) — Linux manual page <https://man7.org/linux/man-pages/man1/tmux.1.html>`__

Terminate a session
~~~~~~~~~~~~~~~~~~~

In order to terminate and close a session, you have to exit from all your opened 
windows inside a session. To do this, you simply ``exit`` on the window terminals
like any other terminal outside tmux. When you close the last window, you will 
see::

    [exited]

on your terminal, and by doing ``tmux ls`` you will not see your session anymore
(or you will see ``no server running on /tmp/tmux-XXXX/default``) if you have no 
tmux session running

Customize Tmux
--------------

You can change your tmux appearance, or change control command keys. You need to 
create a ``.tmux.conf`` file in your home directory, in which define the stuff you need.
Here are some resources where you can find how to customize tmux:

* `Tools I use: Tmux <https://justin.abrah.ms/dotfiles/tmux.html>`__
* `Practical Tmux <https://mutelight.org/practical-tmux>`__
* `Ax example tmux configuration file <https://github.com/bunop/dotfiles/blob/master/tmux/tmux.conf>`__
