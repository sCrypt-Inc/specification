.. _loop:

====
Loop
====
.. code-block:: solidity

    loop (maxLoopCount) [: loopIndex]
        loopBody


Bitcoin script does not provide looping constructs natively for security reasons.
sCrypt achieves looping by repeating the loop body ``maxLoopCount`` times.
For example, the loop

.. code-block:: solidity

    loop (10) {
        x = x * 2;
    }

is equivalently unrolled to

.. code-block:: solidity

    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;
    x = x * 2;


Because `loop unrolling <https://en.wikipedia.org/wiki/Loop_unrolling>`_ is done at compile time, the compiler must know ``maxLoopCount``. That is, it must be a :ref:`compile time constant<ctc-label>`.


If ``maxLoopCount`` is set too small, the contract may not work correctly. If ``maxLoopCount`` is set too large, the resulting script is bloated unnecessarily and costs more to execute.
There are a number of ways to choose the right ``maxLoopCount`` judiciously. One way is to simulate the contract off chain and find the number of loops. Another way is to exploit the 
characteristics of the looping itself. For example, if a loop iterates over each bit of a ``sha256`` hash, ``maxLoopCount`` is ``256``.

.. _induction-var-label:

Induction variable
==================
`Induction variable <https://en.wikipedia.org/wiki/Induction_variable>`_ can be defined when loop index is needed.

.. code-block:: solidity

    // int[3][4] matrix;
    // i & j are induction variables
    loop (3) : i {
        // i is the outer loop index
        loop (4) : j {
            // j is the inner loop index
            matrix[i][j] = i + j;
        }
    }

Conditional loop
================
.. code-block:: solidity

    loop (3) {
        // place condition here
        if (x < 8) {
            x = x * 2;
        }
    }

break
=====
.. code-block:: solidity

    bool done = false;
    loop (3) {
        if (!done) {
            x = x * 2;
            if (x >= 8) {
                done = true;
            }
        }
    }