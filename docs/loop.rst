====
Loop
====
.. code-block:: solidity

    loop (maxLoopCount)
        loopBody


Bitcoin script does not provide looping natively. Script achieves looping by repeating the loop body by the specified ``maxLoopCount`` times.
For example, 

.. code-block:: solidity

    loop (3) {
        x = x * 2;
    }

is converted to

.. code-block:: solidity

    x = x * 2;
    x = x * 2;
    x = x * 2;


Because `loop unrolling <https://en.wikipedia.org/wiki/Loop_unrolling>`_ is done at compile time, the compiler must know ``maxLoopCount``, which has to be a constant number.


If ``maxLoopCount`` is set too small, the contract may not work correctly. If ``maxLoopCount`` is set too large, the resulting script is bloated unnecessarily and costs more to execute.
There are a number of ways to choose the right ``maxLoopCount`` judiciously. One way is to simulate the contract off chain and find the number of loops. Another way is to exploit the 
characteristics of the looping itself. For example, if a loop iterates over each bit of a ``sha256`` hash, ``maxLoopCount`` is ``256``.

Access loop index
=================
.. code-block:: solidity

    int i = 0;
    loop (3) {
        // i becomes the loop index
        i = i + 1;
        x = x * 2;
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