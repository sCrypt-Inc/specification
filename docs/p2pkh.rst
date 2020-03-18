======================
Pay to Public Key Hash
======================
Pay-to-PubKey-Hash (`P2PKH <https://learnmeabitcoin.com/guide/p2pkh>`_) contract is used to send bitcoins to a bitcoin address. It is the most common contract on the Bitcoin network.
Such contracts are unlocked by the public key and a signature created by the corresponding private key.

.. code-block:: solidity

    contract P2PKH {
        Ripemd160 pubKeyHash;

        public function unlock(Sig sig, PubKey pubKey) {
            require(hash160(pubKey) == this.pubKeyHash);
            require(checkSig(sig, pubKey));
        }
    }


