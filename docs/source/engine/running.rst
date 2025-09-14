.. _simulating-games:

Simulating Games
================

.. contents::


Game simulations are typically run via scripts in the ``engine/src`` folder.

The game simulation is described primarily in ``gameplay.py`` and is structured with three processes:

- The process of the first player
- The process of the second player
- The main "ground truth" process of game execution

The reason multithreading is required is due to the potential for player timeouts, runaway recursion, 
and the desire for information compartamentalization. 
Each of the player processes is managed by the main process: they recieve "commands" (i.e. "initialize", "bid", "play")
via queues from the main process: see ``multiprocessing.Queue``.
These the player processes recieve these commands (and their corresponding arguments/data) and run the relevant
functions with these commands
(i.e. an "initialize" would call the constructor, a "play" would call the "play" function and return the turn the player wants to play). The 
returned results of these functions are passed back to the main process via a queue.

If each player initializes properly, the board is copied by the main process and sent to player A's process. Player A's process
returns the actions they want to play and the board is mutated as such. If the game has not ended the board is copied and sent to 
Player B's process, so on and so forth. During Player A's execution Player B's process is paused so as not to take up resources,
and the same is true vice versa.

Note that there are issues having individual processes understand what classes imported in other processes are. This is because Linux
has shared parent process-child process memory space and Windows does not, thus reimporting and ``importlib`` and ``sys.modules``
dependency wrangling is often necessary, see the code for more.


Map Generation
--------------
Map generation is done via a script. Either the map can be regenerated from a history 
(which stores spawns as well - might be changed to a seed instead) for saving and replay, 
or the spawns are generated via a spawning algorithm that takes in map parameters.

Timing system
-------------
On a construction or play call, one time is measured in the player process as close to the beginning of the desired function execution as possible, and another time is measured
in the player process as soon as the desired function returns. The amount of time the function takes to return is the difference taken. This time is reported back to the main process
along with the main return of the function.

Player timeouts are enforced via a ``queue.get`` from the main running process. If the player process does not return in the allotted time (the player's time left + extra return time),
it is assumed the player timed out and the player process is shut down.

.. _sandboxing:
Sandboxing
----------
In order to prevent overuse of resources, the following measures have been taken to sandbox user processes. Note that in the process of sandboxing, it is always easier to run code than to prevent code from being run.

- Both player processes and their children remain paused while the main process is executing. The only time they are resumed is for execution of a command, after which they are immediately paused again.
- Memory limits for player processes are set via ``resource.setrlimit``. Also, after the process returns, the total amount of memory a process is currently taking is checked - if it is over the limit, the player loses. Docker environments are only provisioned a certain amount of RAM as well.
- When player processes are initialized, a number of syscalls are disallowed via ``seccomp`` which comes native to Linux. This includes calls related to filesystems, networking, and kernel. Note that file writing permissions cannot be unset because pickling is the method via which inter-process communication occurs.
- When the docker is initialized, all files and folders except for logs and the game environment (where user code is downloaded) is set to permission ``0o555``, meaning no files can be written to folders in the docker container except the game environment (to allow for downloads.)
- In the server.py file, prior to running the game (and by extension, the player code), permissions for the game environment are also ``0o555``, meaning while the game is playing the entire docker filesystem is frozen. These permissions should not be capable of being unset by player code due to ``seccomp`` preventing player processes from changing permissions. Following the end of the match, permissions for the game environment are set back to ``0o777`` (read+write+execute) to allow for new player code to be downloaded and executed.

This sandboxing is only applicable to the competition server, it is not available on clients due to only being applicable on linux environments.

Quick Testing
-------------
Use the ``run_game`` scripts to run different versions of the games. Text interfaces created via the :ref:`game classes<classes>` are available to see a visualization of the game in terminal, as long as the correct arguments are set.

