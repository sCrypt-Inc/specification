.. _ctc-label:

=====================
Compile Time Constant
=====================
A compile time constant (CTC) is a value that can be computed at compile time. There are four types of compile time constants.

* literals
* static constants which are declared as contract properties and initialized with a literal
* static constants which are declared as function parameters and initialized with another CTC in a function call
* :ref:`induction variables<induction-var-label>`

There are four cases where only compile time constants are allowed.

* loop bound
* array size
* ``size`` in ``reverseBytes(bytes b, static const int size)``
* ``size`` in ``repeat(T e, static const int size)``

.. code-block:: solidity
    
    contract CTC {
        static const int N = 4;
        static const int LOOPCOUNT = 30;

        // A is not a CTC because the right hand size is an expression, not a literal
        static const int A = 2 + 1;
        // B is not a CTC because it is not static
        const int B;

        // FC is a CTC declared in function parameters
        // it can be used within this function, including parameters after it & return type
        function incArr(static const int FC, int[FC] x) : int[FC] {
            loop(FC): i {
                x[i] += i; // induction variable CTC
            }
            return x;
        }

        public function unlock(int y) {
            int[N] arr0 = [1, 2, 3, 4];
            // use `N` to initialize CTC parameter `FC` of function `incArr`
            int[N] arr1 = this.incArr(N, repeat(1, N));
            loop(N) : i {
                require(arr0[i] == arr1[i]);
            }

            int z = 0;
            loop (LOOPCOUNT) 
            {
                if (z<y) z += 4;
            }
            require(y == 1);
        }
    }