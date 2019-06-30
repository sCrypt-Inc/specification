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

        function equal(int y) external {
            require(y == x);
        }
    }

Constructor
===========
Each contract has one and only one constructor. It is where contract member variables are initantiated. 
For example, it can initialize the public key hash of a P2PHK contract, or the hash of a secret in a hash puzzle contract.

require
=======
The ``require`` function specifies terms/conditions of the contract. It consumes a boolean condition and will abort the execution of a contract if the condition is not met.
Otherwise, it will continue the execution.

External Function
=================
Each contract has at least one external function. It is denoted with the ``external`` keyword and does not return any value. The function body corresponds to unlocking script (commonly referred to as ``scriptPubKey``) and its arguments locking script (aka, ``scriptSig``).
External function can be regarded as the entry point into the contract from the outside (like ``main`` in ``C`` and ``Java``). A contract can only be fulfilled and successfully complete when its external function succeeds.

An external function must end with a ``require`` call. ``require`` can also appear in other parts of an external function. An external function only succeeds if it runs to completion without violating any condition in ``require``.
In the above example, ``scriptSig`` of ``10`` can fulfill the contract. 

Multiple External Functions
---------------------------
A contract can have multiple external functions, representing different ways to fulfill a contract. Only one of the external functions can be chosen at a time. In this case, the last operator of ``scriptSig`` has to be the index of the external function chosen, starting from 1.
For example, if ``larger`` external function path is chosen, the ``scriptSig`` of ``10 3`` can fulfill the contract below, in which ``3`` is the external function index.

.. code-block:: solidity

    contract Test {
        int x;

        constructor() {
            x = 10;
        }

        function equal(int y) external {
            require(y == x);
        }

        function smaller(int y) external {
            require(y < x);
        }

        function larger(int y) external {
            require(y > x);
        }
    }
