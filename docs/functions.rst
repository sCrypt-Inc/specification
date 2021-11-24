=========
Functions
=========

User-Defined Functions
======================
sCrypt enables developers to define their own functions as exemplified below:

.. code-block:: solidity

    function sum(int a, int b): int {
        return a + b;
    }

They are only visible within the contract, similar to ``private`` functions in Solidity.

public function
---------------
A public function returns ``true`` if it runs to completion and ``false`` otherwise. 
It does not have return type and ``return`` parts, as they are included implicitly. In other words, 

.. code-block:: solidity

    public function isZero(int a) {
        require(a == 0);
    }

is functionally equivalent to

.. code-block:: solidity

    public function isZero(int a): bool {
        require(a == 0);
        return true;
    }

static function and property
----------------------------
A static property/function can be referenced with contract name as prefix without an instantiated contract, similar to a static function/property in Javascript or C++.
A static property/function can also be referenced without the contract prefix, but only in the contract it is defined in.

.. code-block:: solidity

    library Foo {
        static int N = 0;

        static function incByN(int a): int {
            // N is used with and without Foo prefix
            return a + Foo.N + N;
        }

        static function double(int x): int {
            // incByN() is called with prefix and without
            return Foo.incByN(x) + incByN(x);
        }
    }

    contract Bar {
        public function unlock(int y) {
            require(y == Foo.double(2));
            require(y == Foo.N);
            // N cannot be referenced without Foo prefix
            // require(y == N);
        }
    }

``return``
----------
Due to the lack of native ``return`` semantics support in script, a function currently must end with a ``return`` statement and it is the only valid place for a ``return`` statement.
This requirement may be relaxed in the future. This is usually not a problem since it can be circumvented as follows:

.. code-block:: solidity

    function abs(int a): int {
        if (a > 0) {
            return a;
        } else {
            return -a;
        }
    }

can be rewritten as 

.. code-block:: solidity

    function abs(int a): int {
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
Recursion is disallowed. A function cannot call itself in its body, either directly or indirectly.


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

* ``Sha256 flattenSha256(T a)``

  Returns a Sha256 bytes for the given argument ``a`` of any type. ``T`` here is a generic type which can be any basic type or user-defined struct type.
  If `T` is a basic type, like ``bool`` / ``int`` / ``bytes``,  the return is the same as ``sha256(a)``.
  Otherwise connect all the sha256 values for each flattened fields of `a` to form a joint bytes, and then call `sha256` on it to get the final result.
  

Signature Verification
----------------------
* ``bool checkSig(Sig sig, PubKey pk)``
  
  Returns true if the signature matches the public key. Returns false if the signature is an empty byte array.
  Otherwise, the entire contract fails immediately, due to the `NULLFAIL rule <https://github.com/bitcoin/bips/blob/master/bip-0146.mediawiki#NULLFAIL>`_.

* ``bool checkMultiSig(Sig[M] sigs, PubKey[N] pks)``
  
  Returns true if and only M signatures match M out of N public keys. M and N can be any number as long as M <= N.
  Returns false if all signatures are an empty byte array.
  Otherwise, the entire contract fails immediately.

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

* ``len()``
  Returns the length.

    .. code-block:: solidity

        int a = len(b'ffee11'); // a == 3

* **Slicing Opeartor** - ``b[start:end]`` returns subarray of ``b`` from index ``start`` (inclusive) to ``end`` (exclusive). 
  ``start`` is ``0`` if omitted, ``end`` is length of array if omitted.

    .. code-block:: solidity

        bytes b = b'0011223344556677';
        // b[3:6] == b'334455'
        // b[:4] == b'00112233'
        // b[5:] = b'556677'

* **Concatenation**

    .. code-block:: solidity

        bytes b = b'00112233' + b'334455'  // b == b'00112233334455'

* ``reverseBytes(bytes b, static const int size)``

  Returns reversed bytes of ``b``, which is of ``size`` bytes. Note ``size`` must be a :ref:`compile time constant<ctc-label>`.
  It is often useful when converting a number between little-endian and big-endian.

    .. code-block:: solidity

        // returns b'6cfeea2d7a1d51249f0624ee98151bfa259d095642e253d8e2dce1e79df33f79'
        reverseBytes(b'793ff39de7e1dce2d853e24256099d25fa1b1598ee24069f24511d7a2deafe6c', 32)