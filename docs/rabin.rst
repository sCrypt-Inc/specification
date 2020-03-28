===============
Rabin Signature
===============

`Rabin signature <https://medium.com/@xiaohuiliu/access-external-data-from-bitcoin-smart-contracts-2ecdc7448c43>`_ is an alternative form of digital signature to ECDSA used in Bitcoin.

.. code-block:: solidity

    contract RabinSignature {
        public function verifySig(int sig, bytes msg, bytes padding, int n) {
            int h = this.fromLEUnsigned(this.hash(msg ++ padding));
            require((sig * sig) % n == h % n);
        }

        function hash(bytes x) returns (bytes) {
            // expand into 512 bit hash
            bytes hx = sha256(x);
            int idx = length(hx) / 2;
            return sha256(hx[:idx]) ++ sha256(hx[idx:]);
        }

        function fromLEUnsigned(bytes b) returns (int) {
            // append positive sign byte. This does not hurt even when sign bit is already positive
            return unpack(b ++ b"00");
        }
    }