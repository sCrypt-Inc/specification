====================
Syntax Specification
====================

Formal Specification
====================
.. math::

    \begin{align*}
    contract &::= \mathrm{contract}\ \mathrm{ID}\ \{\ [var]^*\ constructor\ [function]^*\ [public]^+\ \}\\
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
            &\ \ \ |\ \ \{\ [stmt]^+\ \}\\
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

Most of the syntax is self explantory. Syntax unique to Scrypt will be covered later.

Line comment starts with ``//`` and block comment comes between ``/*`` and ``*/``.

Types
=====
Basic Types
-----------

* **bool** - a boolean value.
* **int** - a 32-bit signed integer. Literals of this type can be specified in dec or hex.

    .. code-block:: solidity

        int a = -42;
        int b = 0xab12;

* **bytes** - an array of bytes. Literals of this type are specified in hex.

    .. code-block:: solidity

        bytes arr = 0xffee1234;

Domain-Spcecific Types
----------------------

To increase safety, Scrypt introduces meaningful types that help with catching semantic errors at compile time.
These types add meaning to a raw ``bytes``.
They must be explicitly casted from ``bytes`` with a type constructor.

* **PubKey** - represents a public key.

    .. code-block:: solidity

        PubKey pubKey = PubKey(0x123456789abcdeffedcba987654321);

* **Sig** - represents a tx signature (which can be checked with ``checkSig``).

    .. code-block:: solidity

        Sig sig = Sig(0x123456789abcdeffedcba987654321);

* **Ripemd160** - represents a result of RIPEMD-160 hash.

    .. code-block:: solidity

        Ripemd160 h = hash160(pubKey);

* **Sha1** - represents a result of SHA-1 hash.

    .. code-block:: solidity

        Sha1 h = sha1(secret);

* **Sha256** - represents a result of SHA-256 hash.

    .. code-block:: solidity

        Sha256 h = hash256(secret);


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
      - ``.``
      - left-associative

    * - 5
      - ``< <= > >=``
      - left-associative

    * - 6
      - ``== === != !==``
      - left-associative

    * - 7
      - ``&``
      - left-associative

    * - 8
      - ``^``
      - left-associative

    * - 9
      - ``|``
      - left-associative

    * - 10
      - ``&&``
      - left-associative

    * - 11
      - ``||``
      - left-associative
..
    explain ===,!==,.,@ meaning, and note &&,|| evaluates both sides regardless


Scoping
=======
Scoping in Scrypt follows the prevailing scoping rules of C99 and Solidity.