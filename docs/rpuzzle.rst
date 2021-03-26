========
R-Puzzle
========

In R-puzzle, an ephemeral key ``k`` is never revealed. Instead ``r``, the x coordinate of its corresponding public key, 
is revealed and from ``r`` along with the signature, the knowledge of ``k`` can be proved using existing ``checkSig``.
More information can be found in the `R-Puzzle`_ talk.

.. code-block:: solidity

    contract RPuzzle {
        Ripemd160 rhash;

        constructor(Ripemd160 rhash) {
            this.rhash = rhash;
        }

        function getSigR(Sig sigr): bytes {
            bytes lenBytes = sigr[3:4];
            int len = unpack(lenBytes);
            bytes r = sigr[4:4+len];
            return r;
        }

        public function unlock(Sig sig, PubKey pubKey, Sig sigr) {
            require(this.rhash == hash160(this.getSigR(sigr)));
            require(checkSig(sigr, pubKey));
            require(checkSig(sig, pubKey));
        }
    }


.. _DER: https://docs.moneybutton.com/docs/bsv-signature.html
.. _R-Puzzle: https://streamanity.com/video/2AZUShrYn34XrG?ref=632cb174-4e88-4a6c-91a6-14a25d6b4f58&t=1376