<!-- 
.. link: 
.. description: 
.. tags: Code, OCaml, draft
.. date: 2014/05/01 17:34:42
.. title: Concurrency
.. slug: concurrency
-->

### OCaml:

* `async` module: 

    the idea of `Deferred.t` and maintain a scheduler
    `Deferred.bind`: `'a Deferred.t` -> ('a -> 'b Deferred.t) -> 'b Deferred.t`; `>>=`
    `Return`: `'a -> 'a Deferred.t`
    `Deferred.map`: `'a Deferred.t` -> ('a -> 'b) -> 'b Deferred.t`; `>>|`; "monad"
    Utop will wait for deferred to be determined
