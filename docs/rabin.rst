===============
Rabin Signature
===============

`Rabin signature <https://nchain.com/app/uploads/2018/09/Rabin-Signatures-in-Bitcoin-Cash.pdf>`_ is an alternative form of digital signature to ECDSA used in Bitcoin.

.. code-block:: solidity

    contract RabinSignature {
        Ripemd160 nHash;

        // nHash = 0x88d9931ea73d60eaf7e5671efc0552b912911f2a
        constructor(Ripemd160 nHash) {
            this.nHash = nHash;
        }

        function hash3072(bytes x) returns (bytes) {
            Sha256 y = sha256(x);

            int i = 0;
            // start with empty
            bytes ret = 0x;
            // divide into 3072 / 256 = 12 slices
            loop (12) {
                // 256 bits is 32 bytes
                bytes slice = y[32 * i : 32 * (i + 1)];
                ret = ret ++ sha256(slice);
                i = i + 1;
            }

            // 3072 bit hash
            return ret;
        }

        public function verifySig(int S, bytes U, bytes m, int lambda, bytes n) {
            require(hash160(n) == this.nHash);

            int h = bin2num(this.hash3072(m ++ U));
            require(S * S == h + lambda * bin2num(n));
        }
    }