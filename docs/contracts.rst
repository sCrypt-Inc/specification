==================
Standard Contracts
==================

Multiple Contracts
==================
A single file can define multiple contracts. In this case, the last contract acts as the main contract and is what gets compiled.
Other contracts are dependencies.

In the following example, a standard P2PKH contract is rewritten using two other contracts: a hash puzzle contract that checks the public key matches the public key hash, and a Pay-to-PubKey (P2PK) contract that verifies signature matches public key.

.. code-block:: solidity

    contract HashPuzzle {
        Ripemd160 hash;

        public function spend(bytes preimage) {
            require(hash160(preimage) == this.hash);
        }
    }

    contract Pay2PubKey {
        PubKey pubKey;

        public function spend(Sig sig) {
            require(checkSig(sig, this.pubKey));
        }
    }

    contract Pay2PubKeyHash {
        Ripemd160 pubKeyHash;

        public function spend(Sig sig, PubKey pubKey) {
            HashPuzzle hp = new HashPuzzle(this.pubKeyHash);
            require(hp.spend(pubKey));

            Pay2PubKey p2pk = new Pay2PubKey(pubKey);
            require(p2pk.spend(sig));
        }
    }


import
======
Alternatively, the contract above can be broken into three files. The ``Pay2PubKeyHash`` contact ``import``\s other two contracts as dependencies.
This allows reusing contracts written by others and forms the basis of contract libraries.

A contract can be instantiated by ``new``. A ``public`` function can be called from ``require``, which takes boolean expression as input.

.. code-block:: solidity

    import "./hashPuzzle.scrypt";
    import "./p2pk.scrypt";

    contract Pay2PubKeyHash {
        Ripemd160 pubKeyHash;

        public function spend(Sig sig, PubKey pubKey) {
            HashPuzzle hp = new HashPuzzle(this.pubKeyHash);
            require(hp.spend(pubKey));

            Pay2PubKey p2pk = new Pay2PubKey(pubKey);
            require(p2pk.spend(sig));
        }
    }

Library
=======
A library is the same with a contract, except it does not contain any public function. It is only intended to be imported by a contract or other libraries.
It thus cannot be independently deployed and called. It is frequently used to group related constants and static functions.

.. code-block:: solidity

    library Util {
        // number of bytes to denote some numeric value
        static const int DataLen = 1;
        // number of bytes to denote length serialized state, including varint prefix (1 byte) + length (2 bytes), change length to 4 when you need PushData4
        static const int StateLen = 3;

        // convert signed integer `n` to unsigned integer of `l` bytes, in little endian
        static function toLEUnsigned(int n, int l): bytes {
            // one extra byte to accommodate possible negative sign byte
            bytes m = num2bin(n, l + 1);
            // remove sign byte
            return m[0 : len(m) - 1];
        }
    }

Standard Contracts
==================
sCrypt comes with standard libraries that define many commonly used contracts. They are included by default and do not require explicit ``import`` to be used.

The following example shows usage of the standard contract ``P2PKH`` that corresponds to Pay-to-PubKey-Hash contract.

.. code-block:: solidity

    contract P2PKHStdDemo {
        Ripemd160 pubKeyHash;

        public function unlock(Sig sig, PubKey pubKey) {
            P2PKH p2pkh = new P2PKH(this.pubKeyHash);
            require(p2pkh.spend(sig, pubKey));
        }
    }


Contract ``OP_PUSH_TX``
-----------------------
One grave misconception regarding bitcoin script is that its access is only limited to the data provided in the locking script and corresponding unlocking script.
Thus, its scope and capability are greatly underestimated.

sCrypt comes with a powerful contract called ``Tx`` that allows inspection of the **ENTIRE TRANSACTION** containing the contract itself, besides the locking script and unlocking script.
It can be regarded as a pseudo opcode ``OP_PUSH_TX`` that pushes the current transaction into the stack, which can be inspected at runtime.
More precisely, it enables inspection of the preimage used in signature verification defined in `BIP143`_.
The format of the preimage is as follows:

    1. nVersion of the transaction (4-byte little endian)
    2. hashPrevouts (32-byte hash)
    3. hashSequence (32-byte hash)
    4. outpoint (32-byte hash + 4-byte little endian) 
    5. scriptCode of the input (serialized as scripts inside CTxOuts)
    6. value of the output spent by this input (8-byte little endian)
    7. nSequence of the input (4-byte little endian)
    8. hashOutputs (32-byte hash)
    9. nLocktime of the transaction (4-byte little endian)
    10. sighash type of the signature (4-byte little endian)

As an example, contract ``CheckLockTimeVerify`` ensures coins are timelocked and cannot be spent before ``matureTime`` is reached, similar to `OP_CLTV`_.

.. code-block:: solidity

    contract CheckLockTimeVerify {
        int matureTime;

        public function spend(bytes sighashPreimage) {
            // this ensures the preimage is for the current tx
            require(Tx.checkPreimage(sighashPreimage));
            
            // parse nLocktime
            int l = len(sighashPreimage);
            int nLocktime = this.fromLEUnsigned(sighashPreimage[l - 8 : l - 4]);

            require(nLocktime >= this.matureTime);
        }
        
        function fromLEUnsigned(bytes b): int {
            // append positive sign byte. This does not hurt even when sign bit is already positive
            return unpack(b + b'00');
        }
    }

More details can be found in `this article <https://medium.com/@xiaohuiliu/op-push-tx-3d3d279174c1>`_.
To customize ECDSA signing, such as choosing ephemeral key, there is a more general version called `Tx.checkPreimageAdvanced() <https://medium.com/@xiaohuiliu/advanced-op-push-tx-78ce84f69a66>`_.

Full List
---------

.. list-table::
    :header-rows: 1
    :widths: 20 20 20

    * - Contract 
      - Constructor parameters
      - Public function
    
    * - P2PKH
      - Ripemd160 pubKeyHash
      - spend(Sig sig, PubKey pubKey)

    * - P2PK
      - PubKey pubKey
      - spend(Sig sig)
    
    * - HashPuzzleX [#]_
      - Y [#]_ hash
      - spend(bytes preimage)

    * - Tx
      - None
      - checkPreimage(bytes sighashPreimage)

.. [#] ``X`` is hashing function and can be Ripemd160/Sha1/Sha256/Hash160
.. [#] ``Y`` is hashing function return type and can be Ripemd160/Sha1/Sha256/Ripemd160

.. _BIP143: https://github.com/bitcoin-sv/bitcoin-sv/blob/master/doc/abc/replay-protected-sighash.md
.. _OP_CLTV: https://en.bitcoin.it/wiki/Timelock#CheckLockTimeVerify
