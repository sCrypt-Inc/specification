========
R-Puzzle
========

In R-puzzle, an ephemeral key ``k`` is never revealed. Instead ``r``, the x coordinate its its corresponding public key, 
is revealed and from ``r`` along with the signature, the knowledge of ``k`` can be proved using existing ``checkSig``.

One crucial step in R-Puzzle is to extract ``r`` from `DER`_ encoded signature. The following is much easier than what is presented in the `R-Puzzle`_ talk.

.. code-block:: solidity

    contract RPuzzle {
        Sig s;  // s = b'3045022100948c67a95f856ae875a48a2d104df9d232189897a811178a715617d4b090a7e90220616f6ced5ab219fe1bfcf9802994b3ce72afbb2db0c4b653a74c9f03fb99323f01'

        function getSigR(Sig sig) returns (bytes) {
            bytes lenBytes = sig[3:4];
            int len = unpack(lenBytes);
            bytes r = sig[4:4+len];
            return r;
        }

        // r = b'00948c67a95f856ae875a48a2d104df9d232189897a811178a715617d4b090a7e9'
        public function unlock(bytes r) {
            require(r == this.getSigR(this.s));
        }
    }


.. _DER: https://docs.moneybutton.com/docs/bsv-signature.html
.. _R-Puzzle: https://streamanity.com/video/2AZUShrYn34XrG?ref=632cb174-4e88-4a6c-91a6-14a25d6b4f58&t=1376