Game Logic
==========

.. contents::

Game classes can be found in ``engine/src/game`` in the Engine repo. 
Note that any of the structure defined here will be meant to give insight the design choices of prior years.

Game Definition
---------------
.. code-block:: text

    engine/
    ├── tests/
    └── src/
        ├── script1.py
        └── game/  ----- We discuss elements in this folder, which are used to represent the game
            ├── __init__.py
            ├── board.py
            ├── player_board.py
            ├── game_map.py
            ├── miscclass1.py
            └── miscclass2.py
    

- ``game/board.py``: An objective representation of the board state via composition of classes & operations on those classes. Board state itself is represented as many dictionaries, sets, and arrays that enable fast vectorized operations and O(1) access. Also contains functions for interpreting, interacting with, and forecasting the board state, which should either be O(1) or vectorized and accelerated.
- ``game/player_board.py``: A perspective-based representation of the board state. Done so by wrapping ``Board`` and passing the class variable ``is_player_a`` to the functions in ``Board``. This wrapper is passed to the player so that they can begin their turn seeing the board from their "perspective".
- ``game/maps.json``: A json file mapping map names to string encodings that can be parsed at runtime. Randomized elements require an outside script to parse and generate sequences, see :ref:`how games are run<simulating-games>` for more.
- Other classes: Other classes are designed as supporting elements, typically data structures or representations of the player. These are composed in the  ``Board`` class.


Testing Classes
---------------
We use Pytest to run unit tests on classes. Simply run ``pytest tests`` while in the ``engine`` folder. To run a specific test, run something like ``pytest tests/test_board.py``. Note that all test files have to start with "test" or else when running ``pytest`` it will not be recognized.

``conftest.py`` directly imports the entirety of  ``src`` into the current scope as a module, enabling the imports in the other test files to work (i.e. imports from ``game``).

Autogenerating Documentation for Players
----------------------------------------
