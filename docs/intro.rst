=======================
A Simple Smart Contract
=======================

.. code-block:: solidity
    
    contract Test {
        int x;

        constructor() {
            x = 10;
        }

        destructor equal(int y) {
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

Destructor
==========
Each contract has at least one destructor. Destructor body corresponds to unlocking script (commonly referred to as ``scriptPubKey``) and destructor arguments locking script (aka, ``scriptSig``).
Destructor can be regarded as the entry point into the contract (like ``main`` in ``C`` and ``Java``). A contract can only be fulfilled and successfully complete when its destructor succeeds.

A destructor must end with a ``require`` call. ``require`` can also appear in other parts of a constructor. A destructor only succeeds if it runs to completion without violating any condition in ``require``.
In the above example, ``scriptSig`` of ``10`` can fulfill the contract. 

Multiple Destructors
--------------------
A contract can have multiple destructors, representing different ways to fulfill a contract. Only one of the destructors can be chosen at a time. In this case, the last operator of ``scriptSig`` has to be the index of the destructor chosen, starting from 1.
For example, if ``larger`` destructor path is chosen, the ``scriptSig`` of ``10 3`` can fulfill the contract below, in which ``3`` is the destructor index.

.. code-block:: solidity

    contract Test {
        int x;

        constructor() {
            x = 10;
        }

        destructor equal(int y) {
            require(y == x);
        }

        destructor smaller(int y) {
            require(y < x);
        }

        destructor larger(int y) {
            require(y > x);
        }
    }
