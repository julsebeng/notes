# About Erlang
- Like in math, variables in Erlang are *immutable*.
    - Consequently, like math, calling a function with a particular value will always produce the same output - this is *referential transparency*.
- Erlang emphasizes concurrency and reliability, is designed to
  be fault tolerant, and even supports updating code during execution.

>"Erlang has this very pragmatic approach with functional programming : obey its purest principles (referential transparency, avoiding mutable data, etc), but break away from them when real world problems pop up."

## Actors
The most basic computational entity in Erlang is the *actor*, conceptually similar to how in Java everything is an object.

Fundamentally, actors can perform actions concurrently based on messages  
received. Specifically -
- send a finite number of messages to another actor/actors,
- create a finite number of new actors, or
- specify the behavior of the actor to be used on the next received    
  message.

In Erlang specifically, each actor is a *process* running inside of a virtual machine. These processes are completely isolated from one another, and can only share information via message passing.

## The Erlang environment
Erlang is a functional programming language, but more broadly also a
development environment. Erlang compiles down to bytecode and runs on
a virtual machine like Java, and thus enjoys a measure of portability.

The standard distribution of Erlang includes a compiler, debugger, 
profiler, testing framework, the Open Telecom Platform (OTP), web server,
parser generator, the mnesia database, and more.