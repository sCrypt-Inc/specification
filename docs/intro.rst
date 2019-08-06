=======================
A Simple Smart Contract
=======================

A contract consists of two parts: locking script and unlocking script.

.. code-block:: solidity
    
    contract Test {
        int x;

        constructor() {
            x = 10;
        }

        public function equal(int y) {
            require(y == x);
        }
    }

Constructor
===========
Each contract has one and only one constructor. It is where contract member variables are initialized. 
For example, it can initialize the public key hash of a P2PHK contract, or the hash of a secret in a hash puzzle contract.

require
=======
The ``require`` function specifies terms/conditions of the contract. It consumes a boolean condition and will abort the execution of a contract if the condition is not met.
Otherwise, it will continue the execution.

Public Function
=================
Each contract has at least one public function. It is denoted with the ``public`` keyword and does not return any value. The function body corresponds to unlocking script (commonly referred to as ``scriptPubKey``) and its arguments locking script (aka, ``scriptSig``).
It is visible outside the contract, similar to ``public`` function in Solidity. It acts as the entry point into the contract from the outside (like ``main`` in ``C`` and ``Java``). A contract can only be fulfilled and successfully complete when its public function succeeds.

A public function must end with a ``require`` call. ``require`` can also appear in other parts of a public function. A public function only succeeds if it runs to completion without violating any condition in ``require``.
In the above example, ``scriptSig`` of ``10`` can fulfill the contract. 

Multiple Public Functions
---------------------------
A contract can have multiple public functions, representing different ways to fulfill a contract. Only one of the public functions can be chosen at a time. In this case, the last operator of ``scriptSig`` has to be the index of the public function chosen, starting from 1.
For example, if ``larger`` public function path is chosen, the ``scriptSig`` of ``10 3`` can fulfill the contract below, in which ``3`` is the public function index.

.. code-block:: solidity

    contract Test {
        int x;

        constructor() {
            x = 10;
        }

        public function equal(int y) {
            require(y == x);
        }

        public function smaller(int y) {
            require(y < x);
        }

        public function larger(int y) {
            require(y > x);
        }
    }
