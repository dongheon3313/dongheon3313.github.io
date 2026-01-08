---
title: "[Basics]Understanding Operating Systems, Processes, and Runtime Environments"
categories: dotnet
tags: [Basic, CSharp]
layout: single
share: false
---

**Previous Post**: [[Basics]Understanding C# Build Outputs](https://dongheon3313.github.io/dotnet/Understanding-C-Build-Outputs/)  

---
<br />

## OS, Processes, and Runtime

**Process**: A single program in execution  
**OS**: A program that manages resources to run multiple processes

More precisely, an operating system can be described as *system software that abstracts, protects, and schedules hardware resources so that multiple processes (and threads) appear to run simultaneously.*

To understand the concepts of processes and runtimes, we can simplify many real-world details and observe the following flow when executing an EXE (limited to .NET in this context):

```text
[EXE Execution]
  -> OS loader loads the EXE
  -> EXE declares "I am a .NET application"
  -> CLR (runtime) is loaded
  -> CLR reads IL
  -> JIT converts IL to native code
  -> CLR manages execution, memory, and exceptions
```

However, when you actually run an EXE that you have built on a PC, the process looks more like this:

```text
[1] Process is created
      ->
[2] The execution owner is determined
    - OS loader
    - Host process
    - Runtime host
      ->
[3] An execution engine is acquired
    - Native CPU execution
    - CLR / JVM / other runtimes
      ->
[4] Code is loaded into memory
    - EXE entry point
    - DLL entry point
    - Managed assemblies
      ->
[5] Code is executed according to the rules of the execution engine
```

*Here, a “runtime” refers to the collective environment and engine required while a program is actually running.*  
*In other words, it is the software layer that intervenes at execution time to turn compiled artifacts into a running program.*

The core responsibilities of a runtime include:

- Code loading (EXE / DLL)
- Memory management
- Heap management
- GC (Garbage Collection)
- Function calling convention management
- Exception handling (try/catch)
- Thread management
- Security / access control
- Boundary management with native code (interop)
- JIT compilation when necessary

As shown in the diagrams above, taking C# as an example, build artifacts such as DLLs and EXEs are generated as IL code, and it is the CLR (runtime) that translates this IL into machine code.

At first glance, a runtime may appear similar to an interpreter, but their relationship is one of *containment* (interpreter ⊂ runtime):

- **Interpreter**: “Interpreter”  
- **Runtime**: “Interpreter + driver + seatbelt + navigation system + mechanic”  
<br />

---
**Next Post**: [[Basics]Understanding .NET Runtime Loading in Processes](https://dongheon3313.github.io/dotnet/Understanding-.NET-Runtime-Loading-in-Processes/)
