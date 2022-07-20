
SLURM
=====

.. contents:: Table of Contents

About SLURM
-----------

SLURM is a cluster management and job scheduling system for linux clusters. At
IBBA, we maintain a very small cluster instance which can be used to test pipelines
and scripts by allocating resources and exploiting all the resource we have. Here's
SLURM description from the `official documentation <https://slurm.schedmd.com/quickstart.html>`__:

.. epigraph::

  Slurm is an open source, fault-tolerant, and highly scalable cluster management
  and job scheduling system for large and small Linux clusters. Slurm requires
  no kernel modifications for its operation and is relatively self-contained.
  As a cluster workload manager, Slurm has three key functions. First, it
  allocates exclusive and/or non-exclusive access to resources (compute nodes)
  to users for some duration of time so they can perform work. Second, it provides
  a framework for starting, executing, and monitoring work (normally a parallel job)
  on the set of allocated nodes. Finally, it arbitrates contention for resources
  by managing a queue of pending work.

SLURM (or any other scheduling system) can be used to avoid loading a server more
than its capacity and to reserve and ensure resource which can be used when running
a script or an executable: the scheduler will start a job only when resources are
free and will ensure that no other jobs compete for the same resources.

Using SLURM
-----------

A minimal SLURM cluster installation is composed by a *master* node, which is responsible
to manage job *queues* (or better *partitions* in a SLURM ecosistem), one or more
*working* nodes, which are the resources were jobs are executed and one (or more)
*controller* node, which is the instance where users can submit their jobs. Here at
IBBA we have a *controller* node which is the same host people uses when login
to their remote account, and two working nodes which are used for job execution.
There is also a *master* instance where people can access, however this instance is
very limited in size and must be used only to manage jobs. Moreover, the same
management utilities are available from the controller node, so you don't need to
login into master node to manage your jobs.

About cluster environment
~~~~~~~~~~~~~~~~~~~~~~~~~

The other prerequisite to use slurm is users consistency between controller and
working nodes: in our local installation users are managed through an LDAP server,
are *homes* are mounted through NFS on all cluster nodes: this means that a script
or a software installed and working on the controller host is supposed to work
in the same way even in master and worker nodes. You don't need to move files
from controller node to working nodes, the same environment should be applied
in each cluster nodes.

From here and for the rest of this documentation, we suppose the user logged on
the *controller* node: user can't login to the *working* nodes, with the only
exception of :ref:`interactive jobs <interactive-jobs>`.

Get information on partitions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The *partitions* in SLURM ecosystem are the same as *queues* in other cluster
management, like *PBS*: they group working nodes into logical sets and define
job queues. Each partition can define some constraints, for example cpu or time
limits, user allowed and so on. Priority ordered job are allocated within a partition
until the resources provided by the working nodes are exausted: after that a job
will remain in a pending state, waiting for resource to be available again.

To get information on partitions, you can use the ``sinfo`` command::

  $ sinfo
  PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
  long*        up   infinite      2    mix node[1-2]
  testing      up    4:00:00      2    mix node[1-2]

In this example there are two partitions available: the ``long`` partition is
the default partition (indicated through the ``*`` symbol near its name): this
means that jobs will be executed on this partition when omitting *partition name*
during job submission. ``testing`` partition is another partition which imposes
some restrictions, for example no job can last more than 4 hours. The ``STATE``
columns is important since it gives information about cluster usage. A ``mix``
state like in this example means that the partition is not fully loaded. Other
available stated are ``idle`` which means that the queue is empty, ``alloc`` when
the queue if full and ``down`` when nodes are not available. Please check ``sinfo``
documentation to get a better explanation of such command.

.. hint::

  The SLURM scheduler installed in our infrastructure is more sophisticated than
  a generic FIFO scheduler: a job with a time limit definition can be executed with
  an higher priority than a job without it. Submitting jobs through the ``testing``
  partition can be useful when debugging pipelines or scripts.

Get information on jobs
~~~~~~~~~~~~~~~~~~~~~~~

You can have information about jobs (running/pending) with the ``squeue`` command::

  $ squeue
  JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    559      long nf-bwa_(   cozzip  R    1:34:39      1 node1
    560      long nf-bwa_(   cozzip  R    1:34:39      1 node1
    558      long nf-bwa_(   cozzip  R    1:34:42      1 node1
    557      long nf-bwa_(   cozzip  R    3:32:23      1 node2

The ``ST`` column represents the job *status*: ``R`` means *running* while ``PD``
stand for *pending* job (a job waiting to be executed). You can have also detailed
information with ``sacct`` command by providing the *job ID* through the ``-j``
parameter like in the following example::

  $ sacct -j 557 --format JobID,jobname,NTasks,nodelist,CPUTime,ReqMem,Elapsed
  JobID           JobName   NTasks        NodeList    CPUTime     ReqMem    Elapsed
  ------------ ---------- -------- --------------- ---------- ---------- ----------
  557          nf-bwa_(E+                    node2   14:54:40         8G   03:43:40
  557.batch         batch        1           node2   14:54:40              03:43:40

Get information on resources
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can have detailed information on ``partitions``, ``nodes`` and ``jobs`` with
the ``scontrol show`` command followed by the resource you need.
For example, to collect information on partitions, you can do the following::

  $ scontrol show partitions
  PartitionName=long
    AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
    AllocNodes=ALL Default=YES QoS=N/A
    DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
    MaxNodes=UNLIMITED MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
    Nodes=node[1-2]
    PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
    OverTimeLimit=NONE PreemptMode=OFF
    State=UP TotalCPUs=40 TotalNodes=2 SelectTypeParameters=NONE
    JobDefaults=(null)
    DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED

  PartitionName=testing
    AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
    AllocNodes=ALL Default=NO QoS=N/A
    DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
    MaxNodes=UNLIMITED MaxTime=04:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
    Nodes=node[1-2]
    PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
    OverTimeLimit=NONE PreemptMode=OFF
    State=UP TotalCPUs=40 TotalNodes=2 SelectTypeParameters=NONE
    JobDefaults=(null)
    DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED

If you require information relying on resource name, you can use the proper *name*
after the ``scontrol show <resource>`` command, for example to collect information on
``node1``, you can do the following::

  $ scontrol show nodes node1
  NodeName=node1 Arch=x86_64 CoresPerSocket=8
    CPUAlloc=8 CPUTot=16 CPULoad=8.02
    AvailableFeatures=(null)
    ActiveFeatures=(null)
    Gres=(null)
    NodeAddr=node1 NodeHostName=node1 Version=21.08.5
    OS=Linux 5.15.0-40-generic #43-Ubuntu SMP Wed Jun 15 12:54:21 UTC 2022
    RealMemory=32000 AllocMem=16384 FreeMem=8246 Sockets=2 Boards=1
    State=MIXED ThreadsPerCore=1 TmpDisk=0 Weight=1 Owner=N/A MCS_label=N/A
    Partitions=long,testing
    BootTime=2022-07-08T10:53:31 SlurmdStartTime=2022-07-14T11:00:14
    LastBusyTime=2022-07-20T15:09:05
    CfgTRES=cpu=16,mem=32000M,billing=16
    AllocTRES=cpu=8,mem=16G
    CapWatts=n/a
    CurrentWatts=0 AveWatts=0
    ExtSensorsJoules=n/s ExtSensorsWatts=0 ExtSensorsTemp=n/s

``scontrol`` can also be used to manage cluster entities, however as a final user
you aren't allowed modifying the cluster environment. Please see the ``scontrol``
*manpages* to understand what you can do with this instruction.

Migrating from Torque/PBS to SLURM
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Torque/PBS and SLURM provide similar capabilities, so you can search for documentation
like `Migrating from Torque to Slurm <https://wiki.gacrc.uga.edu/wiki/Migrating_from_Torque_to_Slurm>`__,
`Migrating From PBS <https://docs-research-it.berkeley.edu/services/high-performance-computing/user-guide/running-your-jobs/migrating-from-pbs/>`__
or `PBS to Slurm Conversion Cheat Sheet <https://www.msi.umn.edu/slurm/pbs-conversion>`__
to have a comparison between commands for the two scheduler ecosystem.

Submitting jobs
---------------

Allocate resources and submit a command immediately
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Creating a sbatch script
~~~~~~~~~~~~~~~~~~~~~~~~

.. _interactive-jobs:

Interactive jobs
~~~~~~~~~~~~~~~~

SLURM as Nextflow executor
--------------------------

SLURM can be configured as the default executor for a Nextflow pipeline, using
the environment variable ``NXF_EXECUTOR``::

  $ export NXF_EXECUTOR=slurm

This is sufficient to let Nextflow submit jobs through SLURM controller, without
modifying your pipeline. In alternative simply add ``process.executor = "slurm"``
in the ``nextflow.config`` file. See Nextflow
`SLURM executor documentation <https://www.nextflow.io/docs/latest/executor.html#slurm>`__
to get more information about available options.

.. hint::

  ``NXF_EXECUTOR`` environment variable is already set in our slurm *clients*
