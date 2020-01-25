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

        int a = -42;

* **bytes** - a variable length array of bytes, whose literals are in hex format.

    .. code-block:: solidity

        bytes arr = 0xffee1234;

Subtypes of **bytes**
----------------------

These subtypes are more specific versions of ``bytes``, used to further improve type safety.
To cast a supertype ``bytes`` to them, a function of the type name must be explicitly called.

* **PubKey** - a public key type.

    .. code-block:: solidity

        PubKey pubKey = PubKey(0x123456789abcdeffedcba987654321);

* **Sig** - a signature type.

    .. code-block:: solidity

        Sig sig = Sig(0x123456789abcdeffedcba987654321);

* **Ripemd160** - a RIPEMD-160 hash type.

    .. code-block:: solidity

        Ripemd160 r = hash160(0x123456789abcdeffedcba987654321);

* **Sha1** - a SHA-1 hash type.

    .. code-block:: solidity

        Sha1 s = sha1(0x123456789abcdeffedcba987654321);

* **Sha256** - a SHA-256 hash type.

    .. code-block:: solidity

        Sha256 s = hash256(secr0x123456789abcdeffedcba987654321et);


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
      - ``== === != !==``
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
    explain ===,!==,.,@ meaning, and note &&,|| evaluates both sides regardless


Scoping
=======
Scoping in sCrypt follows the prevailing scoping rules of C99 and Solidity.
The only exception is that we do not allow shadowing names, which may change in the future.
