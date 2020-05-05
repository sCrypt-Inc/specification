=======================
Multiparty Hash Puzzles
=======================

In a hash puzzle contract, the spender has to provide a preimage ``x`` that hashes to a predefined value ``y`` to unlock a UTXO. It can be
extended to multiple parties so that multiple preimages have to be provided such that ``y1 = H(x1)``, ``y2 = H(x2)``, ..., ``yN = H(xN)`` [#]_. Below shows an examples of three parties.

.. code-block:: solidity

    contract MultiPartyHashPuzzles {
        Sha256 hash1;   // hash1 = b'136523B9FEA2B7321817B28E254A81A683D319D715CEE2360D051360A272DD4C'
        Sha256 hash2;   // hash2 = b'E222E30CF5C982E5F6251D755B0B16F608ACE631EB3BA9BDAF624FF1651ABF98'
        Sha256 hash3;   // hash3 = b'2A79F5D9F8B3770A59F91E0E9B4C379F7C7A32353AA6450065E43A8616EF5722'
        
        // preimage1: e.g., "bsv" -> b'627376'
        // preimage2: e.g., "sCrypt" -> b'734372797074'
        // preimage3: e.g., "IDE" -> b'494445'
        public function unlock(bytes preimage1, bytes preimage2, bytes preimage3) {
            require(sha256(preimage1) == this.hash1);
            require(sha256(preimage2) == this.hash2);
            require(sha256(preimage3) == this.hash3);
        }
    }


The above solution is problematic when ``N`` is large since all ``N`` hashes have to be included in the locking script, bloating the transaction. 
Instead, we can combine all ``y``'s into a single y such that ``y = H(H(y1 || y2) || y3)`` [#]_ as shown below.

.. code-block:: solidity

    contract MultiPartyHashPuzzlesCompact {
        // only 1 hash needs to go into the locking script, saving space
        Sha256 combinedHash; // combinedHash = b'C9392767AB23CEFF09D207B9223C0C26F01A7F81F8C187A821A4266F8020064D'

        // preimage1: e.g., "bsv" -> b'627376'
        // preimage2: e.g., "sCrypt" -> b'734372797074'
        // preimage3: e.g., "IDE" -> b'494445'
        public function unlock(bytes preimage1, bytes preimage2, bytes preimage3) {
            Sha256 hash1 = sha256(preimage1);
            Sha256 hash2 = sha256(preimage2);
            Sha256 hash3 = sha256(preimage3);
            Sha256 hash12 = sha256(hash1 ++ hash2);
            Sha256 hash123 = sha256(hash12 ++ hash3);

            require(hash123 == this.combinedHash);
        }
    }


.. [#] ``H`` is a hash function. An online hash calculator can be found `here <https://www.pelock.com/products/hash-calculator>`_.
.. [#] ``||`` is concatenation.