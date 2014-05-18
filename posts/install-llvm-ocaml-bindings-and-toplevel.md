<!-- 
.. link: 
.. description: 
.. tags: Code, Tricks
.. date: 2014/05/06 23:27:24
.. title: Install LLVM OCaml bindings (and UTop)
.. slug: install-llvm-ocaml-bindings-and-toplevel
-->

I put on a [Stackoverflow answer here](http://stackoverflow.com/questions/20002608/llvm-ocaml-bindings)

1. Build LLVM with configuration: `--with-ocaml-libdir = `ocamlc -where``

2. To compile LLVM codes: `ocamlc -cc g++ llvm.cma test.ml -o test`

* To make UTop work: 

    * create a `myutop_main.ml` file with: `let () = UTop_main.main ()`

    * create a custom `llvmutop`: `ocamlfind ocamlmktop -o llvmutop -thread -linkpkg -package utop llvm.cma myutop_main.ml -cc g++`

* If you just want OCaml's build-in toplevel:

    * `ocamlmktop -o llvmtop llvm.cma -cc g++`
