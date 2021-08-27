.. _ctc-label:

=====================
Compile Time Constant
=====================
A compile time constant (CTC) is a value that can be computed at compile time. There are four types of compile time constants.

* literals
* :ref:`induction variables<induction-var-label>`
* static constant properties of a contract, initialized with a literal
* static constant function parameters

There are several cases where only compile time constants are allowed.

* loop bound
* array size
* write to an array element using :ref:`index operator<array-index-label>`
* function parameters that are declared as ``static const`` [#]_ such as ``size`` in ``reverseBytes(bytes b, static const int size)`` and ``repeat(T e, static const int size)``

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

.. [#] Note: the order is important: ``const static`` is not allowed when declaring a function parameter, but allowed when declaring a property.