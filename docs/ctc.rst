.. _ctc-label:

=====================
Compile Time Constant
=====================
A compile time constant (CTC) is a value that can be computed at compile time. There are three types of compile time constants.

* literals
* static constants which are initialized with a literal
* :ref:`induction variables<induction-var-label>`

There are four cases where only compile time constants are allowed.

* loop bound
* array size
* ``size`` in ``reverseBytes(bytes b, int size)``
* ``size`` in ``repeat(T e, int size)``

.. code-block:: solidity
    
    contract CTC {
        static const int N = 4;
        static const int LOOPCOUNT = 30;

        // A is not a CTC because the right hand size is an expression, not a literal
        static const int A = 2 + 1;
        // B is not a CTC because it is not static
        const int B = 2;

        public function unlock(int y) {
            int[N] arr = [1, 2, 3, 4];
            
            int z = 0;
            loop (LOOPCOUNT) 
            {
                if (z<y) z += 4;
            }
            require(y == 1);
        }
    }