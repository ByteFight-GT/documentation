Hosting a game server
=====================

.. contents::


Code for hosting is specified in the ``server.py`` file, along with the ``Dockerfile`` that executes it and the bash scripts use to build and deploy the Docker container. 

Connecting with the Web Server
------------------------------
In order to make the game server capable of taking requests, it has to connect to two elements

1. An online queue (we use RabbitMQ hosted on `CloudAMQP <https://www.cloudamqp.com/>`_), which allows the game server to listen for match requests, and
2. An online storage (currently using `Google Cloud Platform <https://console.cloud.google.com/>`_), but future migration will move this onto self-hosted servers), from which user submissions are downloaded.

Note that both of these should be set in a self-created ``.env`` file at the base of the directory, along with a self-created ``config/gcs_cred.json``. For the content that should exist inside these files,
contact the infrastructure or engine lead for the correct environment variables.

After each match run, the game server places on another queue which the webserver is listening on.

Validation
----------
Validation is performed by instantiating and running one move from the player's controller to see if it returns in time and is a valid move. 
If instantiation, timing, and move validity are all verified, the game server returns a "win" for the player which results in the player's submission being validated by the web server.
Otherwise, the player is issued a "loss" and their submission is marked as invalid.


Building the Engine
-------------------
For testing, you can simply run the ``server.py`` script using python via ``python server.py`` or ``python3 server.py`` and it should execute properly.
However, for `resource constraining and sandboxing <sandboxing>`_ the base of the repository is a ``Dockerfile`` along with supporting elements which are described below.

Note that before running the bash scripts you may have to run ``sudo chmod +x filename`` where filename is replaced by the bash script to make them executable.

- ``docker_requirements.txt``: These are the python packages necessary to deploy a competition-ready game server. Used by the docker container.
- ``requirements.txt``: These are the python packages necessary to just run the game engine, typically a subset of ``docker_requirements.txt``. Used by the game client for packaging the game engine as an executable, see more in :doc:`../client`.
- ``docker_build.sh``: Builds the docker container, should be run after any changes have been made to the codebase.

Deployment
----------
There are a number of steps we take to enforce compute constraints on user processes.

``run_engine_server.sh``:  Should be run after ``docker_build.sh``. 
It takes in either just what you want to name the docker container
as an argument (i.e. ``./run_engine_server.sh bytefight_server_1``) 
or a set of arguments, ordered as follows ``./run_engine_server.sh bytefight_server_2 0-3 1.5g``. 
Again, ``bytefight_server_2`` is what you want to name the docker container,  ``0-3`` means you are only making 
cpu cores 0, 1, 2, and 3 available to that docker container, and ``1.5g`` means you are allocating 1.5 gigabytes of RAM.

You can see different ways of specifying memory and cpu core constraints here at 
`Docker Constraints <https://docs.docker.com/engine/containers/resource_constraints/>`_. Feel free to change the 
``run_engine_server.sh`` to suit your resource constraining needs.

You can check what docker containers are running and their status by running ``docker ps -a``

In order to find out the cores your computer has, install ``htop`` and run it. In order to ensure maximum compute and fairness for competitors, we also attempt to constrain
the OS to specific cores. While this is technically impossible due to the requirement of the OS managing CPU core interrupts, we can move 95% to 99% of OS workload off of
cores that we want running our docker containers.  We do this by modifying the startup and services manager for Linux, ``systemd``, to start user processes on specific cores. 
In doing so, all non-kernel processes spawned will only exist on and have access to the specified cores. This can be accomplished as follows:

1. Edit ``systemd/system.conf`` via something like ``sudo vim /etc/systemd/system.conf`` or ``sudo gedit /etc/systemd/system.conf``
2. Change and uncomment ``CPUAffinity=``, maybe change it to something like ``CPUAffinity=0-2``, which would pin it to the first three cores.
3. Run ``sudo systemctl daemon-reexec``
4. Restart and check core usage using ``htop``

Note that kernel threads such as the Linux Task Scheduler will still run on other cpus, but are typically very lightweight. 
This is why we only guarentee 95-99% core usage in the competition rules.

A setup for a competition game server on a 12-core computer might be as follows:

- OS on cores 0-3
- Game server 1 on cores 4-7
- Game server 2 on cores 8-11


Managing Student Submissions
----------------------------
Student submissions will be downloaded to the same game environment prior to match runs. Be very careful about managing these packages - python caches modules, meaning if you
don't delete the module from ``sys.modules`` on the main thread and ensure the module wasn't cached, you could end up still running a prior submission's code during a match or during validation. 

Stability issues
----------------
During a prior competition run there were dependency issues with ``python3.8`` and ``OpenSSL`` on ``Ubuntu 22.04``, which is what we deployed on. Servers would randomly crash when listening for matches
very occasionally. Thus, we specified ``--restart unless-stopped`` in ``run_engine_server.sh`` when starting up the docker container. This causes the docker container to automatically restart on crash. 
In such cases logging server actions and errors to ``app.log`` so that you have a record of server actions is useful.