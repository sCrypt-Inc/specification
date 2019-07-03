======================
Pay to Public Key Hash
======================

.. code-block:: solidity

    contract P2PKH {
        Ripemd160 pubKeyHash;

        constructor() {
            pubKeyHash = Ripemd160(0x16475707a0c893698c82eb8d177a8164c0aa1e72);
        }

        public function unlock(Sig sig, PubKey pubKey) {
            require(hash160(pubKey) == pubKeyHash);
            require(checkSig(sig, pubKey));
        }
    }


