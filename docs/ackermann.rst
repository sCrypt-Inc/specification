==================
Ackermann Function
==================

The Ackermann function is a classic example of a recursive function, notable especially because it is not a primitive recursive function. 
It grows very quickly in value, as does the size of its call tree. The Ackermann function is usually defined as follows:

.. math::

    A(m, n) =
    \begin{cases}
    n+1 & \mbox{if } m = 0 \\
    A(m-1, 1) & \mbox{if } m > 0 \mbox{ and } n = 0 \\
    A(m-1, A(m, n-1)) & \mbox{if } m > 0 \mbox{ and } n > 0.
    \end{cases}


sCrypt has devised a way to calculate the value of the Ackermann function using `native scripts`_. But it is definitely non-trivial. Below we present a much simpler version. 

.. code-block:: solidity

    contract Ackermann {
        int a;  // a = 2
        int b;  // b = 1

        function ackermann(int m, int n): int {
            bytes stk = num2bin(m, 1);

            // run this function off chain to get the loop count and set it here
            // e.g., (2, 1) requires 14 loops, (3, 5) 42438
            loop (14) {
                if (len(stk) > 0) {
                    bytes top = stk[0:1];
                    m = unpack(top);

                    // pop
                    stk = stk[1:len(stk)];

                    if (m == 0) {
                        n = n + m + 1;
                    } else if (n == 0) {
                        n = n + 1;
                        m = m - 1;
                        // push
                        stk = num2bin(m, 1) + stk;
                    } else {
                        stk = num2bin(m - 1, 1) + stk;
                        stk = num2bin(m, 1) + stk;
                        n = n - 1;
                    }
                }
            }

            return n;
        }

        // y = 5
        public function unlock(int y) {
            require(y == this.ackermann(this.a, this.b));
        }
    }


.. _native scripts: https://onedrive.live.com/?authkey=%21AMkX_N43zpZknj4&cid=68E98EDCE5760610&id=68E98EDCE5760610%2181946&parId=68E98EDCE5760610%2116494&o=OneUp
