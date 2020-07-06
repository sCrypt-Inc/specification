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

static function and property
----------------------------
A static function/property can be referenced with contract name without an instantiated contract, similar to a static function/property in Javascript or C+.

.. code-block:: solidity

    contract Foo {
        int i;
        static int N = 0;

        static function incByN(int a) returns (int) {
                return a + Foo.N;
        }

        function double(int x) returns (int) {
            return Foo.incByN(x) + this.i;
        }
    }

    contract Bar {
        public function unlock(int y) {
            require(y == Foo.sum(1, 2));
        }
    }

``return``
----------
Due to the lack of native ``return`` semantics support in script, a function currently must end with a ``return`` statement and it is the only valid place for a ``return`` statement.
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

.. Warning:: Indirect recursion detection is currently not implemented. If function A calles function B, which in turn calls A, the compilation process will hang. Care must be taken to avoid doing so.


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

``bytes`` Operations
--------------------
* Convert to and from ``int``

``bytes`` can be converted to ``int`` using function ``unpack``.
Little-endian `sign-magnitude representation <https://www.tutorialspoint.com/sign-magnitude-notation>`_ is used, 
where the most significant bit indicates the sign (``0`` for positive, ``1`` for negative).
``int`` can be converted to ``bytes`` with ``pack``.

    .. code-block:: solidity

        int a1 = unpack(b'36');    // 54 decimal
        int a2 = unpack(b'b6');    // -54
        int a3 = unpack(b'e803');  // 1000
        int a4 = unpack(b'e883');  // -1000
        bytes b = pack(a4);        // b'e883'

* ``bytes num2bin(int num, int size)``

  Converts a number ``num`` into a byte array of certain size ``size``, including the sign bit. It fails if the number cannot be accommodated.

* ``reverseBytes20(bytes b)`` ``reverseBytes32(bytes b)``

  Returns reversed bytes of ``b``, which is of 20/32 bytes. They are often useful when converting a number between little-endian and big-endian.

.. code-block:: solidity

        // returns b'6cfeea2d7a1d51249f0624ee98151bfa259d095642e253d8e2dce1e79df33f79'
        reverseBytes32(b'793ff39de7e1dce2d853e24256099d25fa1b1598ee24069f24511d7a2deafe6c')