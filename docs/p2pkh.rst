======================
Pay to Public Key Hash
======================

.. code-block:: solidity

    contract P2PKH {
        constructor(Sig sig, PubKey pubKey) {
            Ripemd160 pubKeyHash = Ripemd160(0x16475707a0c893698c82eb8d177a8164c0aa1e72);
            require(hash160(pubKey) == pubKeyHash);
            require(checkSig(sig, pubKey));
        }
    }


