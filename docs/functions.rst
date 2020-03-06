=========
Functions
=========

User-Defined Functions
======================
sCrypt enables developers to define their own functions as exemplified below:

.. code-block:: solidity

    function sum(int a, int b) returns (int) {
        return a + b;
    }

They are only visible within the contract, similar to ``private`` functions in Solidity.

public function
---------------
A public function returns ``true`` if it runs to completion and ``false`` otherwise. 
It does not have ``returns`` and ``return`` parts, as they are included implicitly. In other words, 

.. code-block:: solidity

    public function sum(int a) {
        require(a == 0);
    }

is functionally equivalent to

.. code-block:: solidity

    public function sum(int a) returns (bool) {
        require(a == 0);
        return true;
    }

``return``
----------
Due to the lack of native ``return`` symantics support in script, a function currently must end with a ``return`` statement and it is the only valid place for a ``return`` statement.
This requirement may be relaxed in the future. This is usually not a problem since it can be circumvented as follows:

.. code-block:: solidity

    function abs(int a) returns (int) {
        if (a > 0) {
            return a;
        } else {
            return -a;
        }
    }

can be rewritten as 

.. code-block:: solidity

    function abs(int a) returns (int) {
        int ret = 0;

        if (a > 0) {
            ret = a;
        } else {
            ret = -a;
        }
        return ret;
    }

Recursion
---------
Recursion is disallowed. A function cannot call itself in its body.

.. Warning:: Indirect recursion detection is currently not implemented. If function A calles function B, which in turn calls A, the compiltion process will hang. Care must be taken to avoid doing so.


Library Functions
=================
The following functions come with sCrypt and are available globally.

Math
----
* ``int abs(int a)``
* ``int min(int a, int b)``
* ``int max(int a, int b)``
* ``bool within(int x, int min, int max)``

Hashing
-------
* ``Ripemd160 ripemd160(bytes b)``
* ``Sha1 sha1(bytes b)``
* ``Sha256 sha256(bytes b)``
* ``Ripemd160 hash160(bytes b)``

  ripemd160(sha256(b))

* ``Sha256 hash256(bytes b)``

  sha256(sha256(b))

Signature Verification
----------------------
* ``bool checkSig(Sig sig, PubKey pk)``
* ``bool checkMultiSig(Sig[] sigs, PubKey[] pks)``

bytes Operations
----------------
* ``bytes b[start:end]``

  Returns subarray from index ``start`` (inclusive) to ``end`` (exclusive).
  
* ``b1 ++ b2``

  Returns the concatenation of bytes ``b1`` and bytes ``b2``.
  
* ``bytes num2bin(int num, int size)``

  Converts a number ``num`` into a byte array of certain size ``size``, including the sign bit. It fails if the number cannot be accommodated.

* ``int length(bytes b)``

  Returns the length of ``b``.

