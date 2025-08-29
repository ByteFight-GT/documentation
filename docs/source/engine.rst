Engine
======
.. contents::

The *BotFightEngine* repository holds the code necessary to 

1. Define the game to be played along with the functions to mutate the game state.
2. Documentation for how to play the game.
3. Simulate and time two agents playing the game.
4. Host a server that receives requests for and plays online matches.

Requirements
------------
Development of the ByteFight game will require installation of ``Python 3``. Development of game 
servers will require ``docker`` and ``Linux``. It is beneficial, but not required,
to understand  vectorization in ``numpy``, Python ``multithreading``, and the basics of 
operating systems such as core usage, memory allocation, and user permissions.

.. note::
   As ByteFight expands, the addition of GPU and/or C++ code are candidates for implementation. Familiarity with
   ``cuda``, ``pytorch``, ``pthreads``, ``ctypes``, and C++ compilation will be important 
   during development. See `Areas for improvement`_ for more details.

Defining a game
---------------
A couple of rules when thinking of the idea for a game:

* The game must be **computable**, i.e. the latest general-purpose computing CPU should be able to compute a significant number of optimal or near-optimal moves in a 5 to 10-minute time frame.
* It must be **fun**. Nobody plays the game if it isn't fun.
* The game should be **computationally interesting**, i.e. solutions should not be trivial.
* The mechanics should be **cohesive**. Winning the game should be the sum of many parts that work together.

Game simulation
---------------

Timing system
^^^^^^^^^^^^^

Sandboxing
^^^^^^^^^^^^^

Hosting a game server
---------------------

Connecting with the Web Server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Validation
^^^^^^^^^^

Game Server optimization
^^^^^^^^^^^^^^^^^^^^^^^^


Stability issues
^^^^^^^^^^^^^^^^

Autogenerating documentation
----------------------------


Areas for improvement
---------------------
Simulation speed
^^^^^^^^^^^^^^^^




.. autosummary::
   :toctree: generated

