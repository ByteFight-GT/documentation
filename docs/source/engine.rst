Engine
======
.. toctree::
   :maxdepth: 2

   engine/classes
   engine/running
   engine/improvement

.. contents::

The Engine repository holds the code necessary to 

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
   during development. See :doc:`engine/improvement` for more details.

Defining a game
---------------
A couple of rules when thinking of the idea for a game:

* The game must be **computable**, i.e. the latest general-purpose computing CPU should be able to compute a significant number of optimal or near-optimal moves in a 5 to 10-minute time frame.
* It must be **fun**. Nobody plays the game if it isn't fun.
* The game should be **computationally interesting**. Solutions should not be trivial, and the best solutions should require significant theoretical computer science knowledge.
* The mechanics should be **cohesive**. Winning the game should be the sum of many parts that work together.
* **Avoid gimmicks** in the mechanics. The competition exists not for the game mechanics, but to offer students a streamlined opportunity to design AI bots. Make the game fun, but avoid things that detract from rather than build toward this goal.




.. autosummary::
   :toctree: generated

