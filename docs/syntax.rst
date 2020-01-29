====================
Syntax Specification
====================

Formal Specification
====================
.. math::

    \begin{align*}
    contract &::= \mathrm{contract}\ \mathrm{ID}\ \{\ [var]^*\ [constructor]\ [function]^*\ [public]^+\ \}\\
    var &::= formal;\\
    formal &::= \mathrm{TYPE}\ \mathrm{ID}\\
    function &::= \mathrm{function}\ \mathrm{ID}(formal[,\ formal]^*)\ \mathrm{returns}\ (\mathrm{TYPE})\ \{\ [stmt]^*\ \mathrm{return}\ expr;\ \}\\
    constructor &::= \mathrm{constructor}([formal[,\ formal]^*])\ \{\ [stmt]^*\ \}\\
    public &::= \mathrm{public}\ \mathrm{function}\ \mathrm{ID}(formal[,\ formal]^*)\ \{\ [stmt]^*\ \mathrm{require}(expr);\}\\
    stmt &::= \mathrm{TYPE}\ \mathrm{ID} = expr;\\
            &\ \ \ |\ \ \mathrm{ID} = expr;\\
            &\ \ \ |\ \ \mathrm{require}(expr);\\
            &\ \ \ |\ \ \mathrm{if}\ (expr)\ stmt\ [\mathrm{else}\ stmt]\\
            &\ \ \ |\ \ \mathrm{loop}\ (intConst)\ stmt\\
            &\ \ \ |\ \ \{\ [stmt]^*\ \}\\
    expr &::= \mathsf{UnaryOp}\ expr\\
            &\ \ \ |\ \ expr\ \mathsf{BinaryOp}\ expr\\
            &\ \ \ |\ \ \mathrm{ID}(expr[,\ expr]^*)\\
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
* **int** - a signed integer of arbitrary length, whose literals are in decimal format.

    .. code-block:: solidity

        int a1 = 42;
        int a2 = -4242424242424242;
        int a3 = 55066263022277343669578718895168534326250603453777594175500187360389116729240;

* **bytes** - a variable length array of bytes, whose literals are in hex format.

    .. code-block:: solidity

        bytes b1 = 0xffee1234;
        bytes b2 = 0x414136d08c5ed2bf3ba048afe6dcaebafeffffffffffffffffffffffffffffff00;

``bytes`` can be explicitly cast to ``int`` using little-endian `sign-magnitude representation <https://www.tutorialspoint.com/sign-magnitude-notation>`_, 
where the most significant bit indicates the sign (``0`` for positive, ``1`` for negative). ``int`` can also be explicitly cast to ``bytes``.

    .. code-block:: solidity

        int a1 = int(0x36);    // 54 decimal
        int a2 = int(0xb6);    // -54
        int a3 = int(0xe803);  // 1000
        int a4 = int(0xe883);  // -1000
        bytes b = bytes(a4);   // 0xe883


Subtypes of ``bytes``
---------------------

These subtypes are more specific versions of ``bytes``, used to further improve type safety.
To cast a supertype ``bytes`` to them, a function of the type name must be explicitly called.

* **PubKey** - a public key type.

    .. code-block:: solidity

        PubKey pubKey = PubKey(0x0200112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100);

* **PrivKey** - a private key type.

    .. code-block:: solidity

        PrivKey privKey = PrivKey(0x00112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100);

* **Sig** - a signature type in DER format, including hash type.

    .. code-block:: solidity

        Sig sig = Sig(0x3045022100b71be3f1dc001e0a1ad65ed84e7a5a0bfe48325f2146ca1d677cf15e96e8b80302206d74605e8234eae3d4980fcd7b2fdc1c5b9374f0ce71dea38707fccdbd28cf7e41);

* **Ripemd160** - a RIPEMD-160 hash type.

    .. code-block:: solidity

        Ripemd160 r = hash160(0x0011223344556677889999887766554433221100);

* **Sha1** - a SHA-1 hash type.

    .. code-block:: solidity

        Sha1 s = sha1(0x0011223344556677889999887766554433221100);

* **Sha256** - a SHA-256 hash type.

    .. code-block:: solidity

        Sha256 s = hash256(0x00112233445566778899aabbccddeeffffeeddccbbaa99887766554433221100);


Operators
=========

.. list-table::
    :header-rows: 1
    :widths: 20 20 20

    * - Precedence 
      - Operator
      - Associativity 

    * - 1
      - ``- !``
      - right-associative

    * - 2
      - ``* / %``
      - left-associative

    * - 3
      - ``+ -``
      - left-associative

    * - 4
      - ``++``
      - left-associative

    * - 5
      - ``<< >>``
      - left-associative

    * - 6
      - ``< <= > >=``
      - left-associative

    * - 7
      - ``== !=``
      - left-associative

    * - 8
      - ``&``
      - left-associative

    * - 9
      - ``^``
      - left-associative

    * - 10
      - ``|``
      - left-associative

    * - 11
      - ``&&``
      - left-associative

    * - 12
      - ``||``
      - left-associative
..
    explain &&,|| evaluates both sides regardless


Scoping
=======
Scoping in sCrypt follows the prevailing scoping rules of C99 and Solidity.
The only exception is that we do not allow shadowing names, which may change in the future.
