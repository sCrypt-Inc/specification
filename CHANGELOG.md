## sCrypt 0.1.0
#### 05/05/20
* Change `b""` for `bytes` literal to `b''`

#### 02/14/20

* JavaScript bindings for the compiler: [scryptc @npm](https://www.npmjs.com/package/scryptc)

* Multiple contracts in one file

* Support `import` dependencies

* Standard libraries

* Report multiple compiling errors and do not abort at first error

* Support contract `"reflection"` to obtain current tx the contract runs in

* Support `this` to access contract variable and method

* Call contract methods

* Make constructor optional

* Public function also returns like other functions, `returns (bool)` and `return true` by default if not specified.

* Improve error messages significantly to help identify source of compiling error; also aggregate similar errors to reduce verbosity

* Support `***` statement to insert `OP_CODESEPARATOR`

* Support `exit()` to terminate contract execution

* Add `pack()` and `unpack()` to convert between `int` and `bytes`

* Change `0x` prefix to denote `int` literal in hex and `b""` for `bytes` literal

* Rename `size()` to `len()`

* Add type `PrivKey`

* Overload `==` for `int` and `bytes`

* Support `OP_PUSHDATA2` and `OP_PUSHDATA4`

* Improve scryptc CLI
    - output script as json
    - output AST json as well
    - output to a specified directory
    - output dependency ASTs
    - output debug mode for more info, besides script
    - input from stdin

* Numerous bug fixes and refactorings
    - minimally encode `int`
    - lower S in DER signature
    - throw when `OP_PUSHDATA` data exceeding 4GB