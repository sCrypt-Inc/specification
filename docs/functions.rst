=========
Functions
=========

User-Defined Functions
======================
Scrypt enables developers to define their own functions as examplified below:

.. code-block:: solidity

    function sum(int a, int b) returns (int) {
        return a + b;
    }

Return
------
Due to the lack of native ``return`` symantics support in script, a function currently must end with a ``return`` statement and it is the only place for a ``return`` statement. ``OP_RETURN`` has a different meaning, at least for now.

Recursion
---------
Recursion is disallowed. A function cannot call itself in its body.

.. Warning:: Indirect recursion detection is currently not implemented. If function A calles function B, which in turn calls A, the compiltion process will hang. Care must be taken to avoid doing so.


Library Functions
=================
The following functions come with Scrypt and are availabe globally.

Math
----
* ``int abs(int a)``
* ``int min(int a, int b)``
* ``int max(int a, int b)``
* ``bool within(int x, int min, int max)``

Hashing
-------
* ``Ripemd160 ripemd160(bytes bytes)``
* ``Sha1 sha1(bytes bytes)``
* ``Sha256 sha256(bytes bytes)``
* ``Ripemd160 hash160(bytes bytes)``
* ``Sha256 hash256(bytes bytes)``

Signature Verification
----------------------
* ``bool checkSig(Sig sig, PubKey pk)``
* ``bool checkMultiSig(Sig[] sigs, PubKey[] pks)``

bytes Operations
----------------
* ``bytes num2bin(int num, int size)``

  Converts a number ``num`` into a bytes array of size ``size``.

* ``int bin2num(bytes data)``

  Converts a bytes array ``data`` to an integer. The array is treated as little-endian.

* ``int size(bytes data)``

  Returns the length of ``data``.


Type Constructors
=================
Explicit type cast is mandated to convert a general ``bytes`` type to a more specific type.

* ``PubKey PubKey(bytes data)``
* ``Ripemd160 Ripemd160(bytes data)``
* ``Sha1 Sha1(bytes data)``
* ``Sha256 Sha256(bytes data)``
* ``Sig Sig(bytes data)``

