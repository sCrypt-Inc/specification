====================
Syntax Specification
====================

Formal Specification
====================
.. math::

    \begin{align*}
    program &::= [importDirective]^*\ [contract]^+\\
    importDirective &::= \mathrm{import}\ "\mathrm{ID}";\\
    contract &::= \mathrm{contract}\ \mathrm{ID}\ \{\ [var]^*\ [constructor]\ [function]^+\ \}\\
    var &::= formal;\\
    formal &::= \mathrm{TYPE}\ \mathrm{ID}\\
    constructor &::= \mathrm{constructor}([formal[,\ formal]^*])\ \{\ [stmt]^*\ \}\\
    function &::= \mathrm{[public|static]}\ \mathrm{function}\ \mathrm{ID}([formal[,\ formal]^*])\ \mathrm{[returns}\ (\mathrm{TYPE]})\ \{\ [stmt]^*\ \mathrm{[return}\ expr;]\ \}\\
    stmt &::= \mathrm{TYPE}\ \mathrm{ID} = expr;\\
            &\ \ \ |\ \ \mathrm{ID}\ \mathrm{ID} = \mathrm{new}\ \mathrm{ID}(expr^*);\\
            &\ \ \ |\ \ \mathrm{ID} = expr;\\
            &\ \ \ |\ \ \mathrm{require}(expr);\\
            &\ \ \ |\ \ \mathrm{exit}(expr);\\
            &\ \ \ |\ \ \mathrm{if}\ (expr)\ stmt\ [\mathrm{else}\ stmt]\\
            &\ \ \ |\ \ \mathrm{loop}\ (intConst)\ stmt\\
            &\ \ \ |\ \ \{\ [stmt]^*\ \}\\
            &\ \ \ |\ \ \mathrm{CODESEPARATOR}\\
    expr &::= \mathsf{UnaryOp}\ expr\\
            &\ \ \ |\ \ expr\ \mathsf{BinaryOp}\ expr\\
            &\ \ \ |\ \ \mathrm{ID}(expr[,\ expr]^*)\\
            &\ \ \ |\ \ \mathrm{ID}.\mathrm{ID}\\
            &\ \ \ |\ \ \mathrm{ID}.\mathrm{ID}(expr[,\ expr]^*)\\
            &\ \ \ |\ \ \mathrm{ID}\mathbf{[}expr:expr\mathbf{]}\\
            &\ \ \ |\ \ (expr)\\
            &\ \ \ |\ \ \mathrm{ID}\\
            &\ \ \ |\ \ boolConst \\
            &\ \ \ |\ \ intConst \\
            &\ \ \ |\ \ bytesConst \\
    \end{align*}

Most of the syntax is self explanatory. Syntax unique to sCrypt will be covered later.

Line comment starts with ``//`` and block comment comes between ``/*`` and ``*/``.

Types
=====
Basic Types
-----------

* **bool** - a boolean value ``true`` or ``false``.
* **int** - a signed integer of arbitrary length, whose literals are in decimal or hexadecimal format.

    .. code-block:: solidity

        int a1 = 42;
        int a2 = -4242424242424242;
        int a3 = 55066263022277343669578718895168534326250603453777594175500187360389116729240;
        int a4 = 0xFF8C;

* **bytes** - a variable length array of bytes, whose literals are either in quoted hexadecimal format prefixed by ``b``, or double quoted UTF8 string.

    .. code-block:: solidity

        bytes b1 = b'ffee1234';
        bytes b2 = b'414136d08c5ed2bf3ba048afe6dcaebafeffffffffffffffffffffffffffffff00';
        bytes b3 = b'1122' + b'eeff'; // b3 is b'1122eeff'
        bytes str = "hello world"; // utf8 string

Array Types
-----------
An array is a fixed-size list of values of the same basic type.

* **Array Literals** - a comma-separated list of expressions, enclosed in square brackets. Array size must be an integer constant greater than zero.

    .. code-block:: solidity

        bool[3] b = [false, false && true || false, true || (1 > 2)];
        int[3] c = [72, -4 - 1 - 40, 833 * (99 + 9901) + 8888];
        bytes[3] a = [b'ffee', b'11', b'22'];
        int[2][3] d = [[11, 12, 13], [21, 22, 23]];
        // array demension can be omitted when declared
        int[] e = [1, 4, 2];  // e is of type int[3]
        int[][] f = [[11, 12, 13], [21, 22, 23]]; // f is of type int[2][3]

* **Initialize/set an array to the same value** - Function ``T[size] repeat(T e, static const int size)`` returns an array with all ``size`` elements set to ``e``, where T can be any type.
  Note ``size`` must be a :ref:`compile time constant<ctc-label>`.

    .. code-block:: solidity

        // a == [0, 0, 0]
        int[3] a = repeat(0, 3);
        // arr2D == [[0, 0, 0], [0, 0, 0]]
        int[2][3] arr2D = repeat(0, 2);
        int[4] flags = [false, true, false, true]
        // set all flags to be false
        flags = repeat(false, 4);

.. _array-index-label:

* **Index Operator** - index starting from 0. Out of bound access fails contract execution immediately.

    .. code-block:: solidity

        int[3] a = [1, 4, 2];
        int[2][3] arr2D = [[11, 12, 13], [21, 22, 23]];
        int d = a[2];
        a[1] = -4;
        int idx = 2;
        // variable index is allowed when reading an array
        d = a[idx];
        d = arr2D[idx][1];
        // variable index is disallowed when writing into an array
        a[idx] = 2;
        // only a compile-time constant (CTC) can be used as an index when writing
        a[2] = 2;
        a[N] = 3; // N is a CTC
        // assign to an array variable
        a = arr2D[1];
        // b is a new copy and the same as a
        int[3] b = a;
        // two arrays are equal if and only if they are of the same size and all elements are equal
        require(a == b);

Struct Types
------------
A struct (or structure) is a collection of variables (can be of different basic types) under a single name.

* **Define Struct**

    .. code-block:: solidity

        struct Point {
          int x;
          int y;
        }

        struct Line {
          // nested struct
          Point start;
          Point end;
        }

* **Use Struct**
    .. code-block:: solidity
        
        Point p = {10, -10};
        int x = p.x;
        p.y = 20;
        // Define a variable q of type Point, and set members to the same values as those of p
        Point q = p;
        require(p == q); // true
        // nested
        Line l = {p, q};
        l.start.x = l.end.y + 1;

Type Inference
--------------
The ``auto`` keyword specifies that the type of the variable, of basic type, declared will be automatically deducted from its initializer.

    .. code-block:: solidity

        auto a1 = b'36';      // bytes a1 = b'36';
        auto a2 = 1 + 5 * 3;  // int a2 = 1 + 5 * 3;

Type Aliases
------------
Type aliases create a new name for a type. It does not actually create a new type, it merely creates a new name to refer to that type.

    .. code-block:: solidity

        type Age = int;
        type Coordinate = int[2];

Generics/Generic Types
----------------------
A generic type is a parameterized type. It allows a library to work over a variety of types rather than a single one.
Users can consume these libraries and use their own concrete types.

* **Declare Generic Types**

Generic types can only be declared at library level and used within the library's scope. 

    .. code-block:: solidity

        // declare two generic type variables: K & V
        library HashedMap<K, V> {

          // use them as function parameters' type
          function set(K k, V v, int idx) { 
            ...
          }

        }

* **Instantiate Generic Types**

    .. code-block:: solidity

        HashedMap<bytes, int> map = new HashedMap();
        map.set(b'01', 1, 0);
        map.set(2, 1, 1); // this will throw semantic error for the first argument's type `int`, which expects `bytes` 

Domain Subtypes
===============
There are several subtypes, specific to the Bitcoin context, used to further improve type safety.

Subtypes of ``bytes``
---------------------
To cast a supertype ``bytes`` to them, a function of the type name must be explicitly called.

* **PubKey** - a public key type.

    .. code-block:: solidity

        PubKey pubKey = PubKey(b'0200112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100');

* **Sig** - a signature type in `DER <https://docs.moneybutton.com/docs/bsv-signature.html>`_ format, including `signature hash type <https://github.com/libbitcoin/libbitcoin-system/wiki/Sighash-and-TX-Signing>`_, which is ``SIGHASH_ALL | SIGHASH_FORKID`` (``0x41``) in the below example.

    .. code-block:: solidity

        Sig sig = Sig(b'3045022100b71be3f1dc001e0a1ad65ed84e7a5a0bfe48325f2146ca1d677cf15e96e8b80302206d74605e8234eae3d4980fcd7b2fdc1c5b9374f0ce71dea38707fccdbd28cf7e41');

* **Ripemd160** - a RIPEMD-160 hash type.

    .. code-block:: solidity

        Ripemd160 r = Ripemd160(b'0011223344556677889999887766554433221100');

* **PubKeyHash** - an alias for `Ripemd160`` type. Usually represent a bitcoin address.

    .. code-block:: solidity

        PubKeyHash aliceAddress = PubKeyHash(b'0011223344556677889999887766554433221100');

* **Sha1** - a SHA-1 hash type.

    .. code-block:: solidity

        Sha1 s = Sha1(b'0011223344556677889999887766554433221100');

* **Sha256** - a SHA-256 hash type.

    .. code-block:: solidity

        Sha256 s = Sha256(b'00112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100');

* **SigHashType** - a sighash type.

    .. code-block:: solidity

        SigHashType s = SigHashType(b'01');
        SigHashType s = SigHash.ALL | SigHash.ANYONECANPAY;

* **SigHashPreimage** - a sighash preimage type.

    .. code-block:: solidity

        SigHashPreimage s = SigHashPreimage(b'0100000028bcef7e73248aa273db19d73');

* **OpCodeType** - a OpCode type.

    .. code-block:: solidity

        OpCodeType s = OpCode.OP_DUP + OpCode.OP_ADD;

Subtypes of ``int``
-------------------

* **PrivKey** - a private key type.

    .. code-block:: solidity

        PrivKey privKey = PrivKey(0x00112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100);

        
``const`` Variables
===================
Variables declared const cannot be changed once initialized.

.. code-block:: solidity

    contract Test {
        const int x;

        constructor(int x) {
            this.x = x; // good, since this is initialization
        }

        public function equal(const int y) {
            y = 1; // <-- error
            
            const int a = 36;
            a = 11; // <-- error
            
            require(y == this.x);
        }
    }

    
``if`` statement
================
``if`` condition can be of type ``int`` and ``bytes``, besides ``bool``. They are implicitly converted to ``bool`` as in C and Javascript.
An ``int`` expression is evaluated to ``false`` if and only if it is ``0`` (including negative ``0``).
A ``bytes`` expression is evaluated to ``false`` if and only if every of its byte is ``b'00'`` (including empty ``bytes`` ``b''``).

    .. code-block:: solidity

      int cond = 25; // true
      int cond = 0;  // false
      int cond = unpack(b'80') // false since it is negative 0
      int cond = unpack(b'000080') // false since it is negative 0
      if (cond) {} // equivalent to if (cond != 0) {}
      
      bytes cond = b'00'; // false
      bytes cond = b''; // false
      bytes cond = b'80'; // true. Note b'80' is treated as false if converted to int
      bytes cond = b'10' & b'73'; // true since it evaluates to b'10'
      if (cond) {}


exit()
======
``exit(bool status);`` statement terminates contract execution. If ``status`` is ``true``, contract succeeds; otherwise, it fails.

    .. code-block:: solidity

      contract TestPositiveEqual {
          int x;

          constructor(int x) {
              this.x = x;
          }

          public function equal(int y) {
              if (y <= 0) {
                exit(true);
              }
              require(y == this.x);
          }
      }


Code Separator
==============
Three or more ``*`` in a line inserts an `OP_CODESEPARATOR <https://en.bitcoin.it/wiki/OP_CHECKSIG#How_it_works>`_. It is used to exclude what comes before (and including itself) it from being part of the signature.
Note there is no ``;`` at the end.

    .. code-block:: solidity

      contract P2PKH_OCS {
          Ripemd160 pubKeyHash;

          public function unlock(Sig sig, PubKey pubKey) {
              // code separator 1
              ***
              require(hash160(pubKey) == this.pubKeyHash);
              // code separator 2
              *****
              require(checkSig(sig, pubKey));
          }
      }

Access Modifiers
================
There are three types of access modifiers available to help restrict the scope of properties and functions of a contract:

* Default: no keyword required
* Private
* Public: only applies to functions

Only public functions can be called externally by Bitcoin transactions.

.. list-table::
    :header-rows: 1
    :widths: 20 20 20 20

    * - 
      - default 
      - private
      - public

    * - Same contract
      - Yes
      - Yes
      - Yes

    * - Other contract
      - Yes
      - No
      - Yes

    * - Externally
      - No
      - No
      - Yes

Operators
=========

.. list-table::
    :header-rows: 1
    :widths: 20 40  20  20



    * - Precedence 
      - Operator                      
      - Associativity 
      - Note
    
    * - 1
      - ``() ++ -- .``
      - left-to-right
      - 
    * - 2
      - ``[]``
      - left-to-right
      - 
    * - 3
      - ``++ -- - ! ~``
      - right-to-left
      - 

    * - 4
      - ``* / %``
      - left-to-right
      -

    * - 5
      - ``+ -``
      - left-to-right
      - 

    * - 6
      - ``<< >>``
      - left-to-right
      - The number of bits to shift must be non-negative, otherwise evaluation fails immediately

    * - 7
      - ``< <= > >=``
      - left-to-right
      - 

    * - 8
      - ``== !=``
      - left-to-right
      - 

    * - 9
      - ``&``
      - left-to-right
      - In the case of two integers with different length, the shorter one with be expanded first to be the same legnth with the longer one using `num2bin`

    * - 10
      - ``^``
      - left-to-right
      - Same as ``&``

    * - 11
      - ``|``
      - left-to-right
      - Same as ``&``

    * - 12
      - ``&&``
      - left-to-right
      -

    * - 13
      - ``||``
      - left-to-right
      - 

    * - 14
      - ``? :``
      - right-to-left
      - 

    * - 15
      - ``+= -= *= /= %= &= |= ^= <<= >>=``
      - right-to-left
      - 

.. Note:: 
    - Operator ``&&``, ``||``, and ``? :`` use `short-circuit evaluation <https://en.wikipedia.org/wiki/Short-circuit_evaluation>`_.
    - After performing bitwise on integers, such as operator ``&``, ``|``, ``^``, and ``~``, the compiler uses ``OP_BIN2NUM`` to compress the results.

Scoping
=======
Scoping in sCrypt follows the prevailing scoping rules of C99 and Solidity.
Outer scope variable is shadowed by the inner scope variable of the same name.
