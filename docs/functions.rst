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

Return
------
Due to the lack of native ``return`` symantics support in script, a function currently must end with a ``return`` statement and it is the only place for a ``return`` statement. ``OP_RETURN`` has a different meaning, at least for now.

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

  Returns subarray from index ``start`` inclusive to ``end`` exclusive.
  
* ``b1 ++ b2``

  Returns the concatenation of bytes ``b1`` and bytes ``b2``.
  
* ``bytes num2bin(int num, int size)``

  Converts a number ``num`` into a bytes array of size ``size``.

* ``int bin2num(bytes b)``

  Converts a bytes array ``b`` to an integer. The array is treated as little-endian.

* ``int size(bytes b)``

  Returns the length of ``b``.


Type Casting
============
Explicit type cast is mandated to convert a general ``bytes`` type to a more specific type.

* ``PubKey PubKey(bytes b)``
* ``Ripemd160 Ripemd160(bytes b)``
* ``Sha1 Sha1(bytes b)``
* ``Sha256 Sha256(bytes b)``
* ``Sig Sig(bytes b)``

