========
R-Puzzle
========

In R-puzzle, an ephemeral key ``k`` is never revealed. Instead ``r``, the x coordinate its its corresponding public key, 
is revealed and from ``r`` along with the signature, the knowledge of ``k`` can be proved using existing ``checkSig``.

One crucial in R-Puzzle is to extract ``r`` from `DER`_ encoded signature. The following is much easier than what is presented in the `R-Puzzle`_ talk.

.. code-block:: solidity

    contract RPuzzle {
        function getSigR(Sig sig) returns (bytes) {
            bytes (_, tail) = sig @ 3;
            bytes (len, tail2) = tail @ 1;
            bytes (r, _) = tail2 @ bin2num(len);
            return r;
        }

        // r = 00948c67a95f856ae875a48a2d104df9d232189897a811178a715617d4b090a7e9
        constructor(bytes r) {
            Sig sig = Sig(0x3045022100948c67a95f856ae875a48a2d104df9d232189897a811178a715617d4b090a7e90220616f6ced5ab219fe1bfcf9802994b3ce72afbb2db0c4b653a74c9f03fb99323f01);
            require(r == getSigR(sig));
        }
    }


.. _DER: https://docs.moneybutton.com/docs/bsv-signature.html
.. _R-Puzzle: https://streamanity.com/video/2AZUShrYn34XrG?ref=632cb174-4e88-4a6c-91a6-14a25d6b4f58&t=1376