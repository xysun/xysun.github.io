<!-- 
.. link: 
.. description: 
.. tags: draft
.. date: 2014/03/06 23:27:24
.. title: Install LLVM OCaml bindings (and toplevel)
.. slug: install-llvm-ocaml-bindings-and-toplevel
-->

LLVM configuration: `--with-ocaml-libdir = `ocamlc -where``

Compile: `ocamlc -cc g++ llvm.cma test.ml -o test`

UTop: 

* `let () = UTop_main.main ()`

* `ocamlfind ocamlmktop -o llvmutop -thread -linkpkg -package utop llvm.cma myutop_main.ml -cc g++`
