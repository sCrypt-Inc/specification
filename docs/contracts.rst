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
Alternatively, the contact above can be broken into three files. The ``Pay2PubKeyHash`` contact ``import``\s other two contracts as dependencies.
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

.. [#] ``X`` is hashing function and can be Ripemd160/Sha1/Sha256/Hash160
.. [#] ``Y`` is hashing function return type and can be Ripemd160/Sha1/Sha256/Ripemd160