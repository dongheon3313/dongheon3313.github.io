---
title: "[Basics]Understanding .NET Runtime Loading in Processes"
categories: dotnet
tags: [Basic, CSharp]
layout: single
share: false
---

**Previous Post**: [[Basics]Understanding Operating Systems, Processes, and Runtime Environments](https://dongheon3313.github.io/dotnet/Understanding-Operating-Systems,-Processes,-and-Runtime-Environments/)  
  
---
<br />

## How Runtime is Determined When a Process Starts

When a process starts up, the runtime is either "loaded" or "determined" as shown below. In other words, the runtime can be determined at "build time" or at "load time."
  
### Process Structure

```
[Process]
 ├─ clr.dll          ← Core of execution
 ├─ yourapp.exe
 ├─ mscorlib.dll
 └─ Other modules
```
<br />

## Runtime Determination by Deployment Type

### Build-Time Runtime Determination

The CLR (Common Language Runtime) is determined at **build time** for:
- .NET Framework-based EXE or DLL files
- Self-contained EXE applications
- Native AOT EXE applications

### Load-Time Runtime Determination

The CLR is determined at **execution/load time** for:
- .NET Core / .NET 5+ based EXE or DLL files
- More precisely, the runtime is determined by searching for installed runtimes on the system
<br />

## Deployment Model Comparison

### Self-contained EXE
- **Pros**: Minimal dependency on the execution environment
- **Cons**: Larger deployment size

### Native AOT EXE
- **Pros**: Includes only the minimal runtime required, enabling the **fastest startup** time
- **Cons**: Limited reflection and dynamic capabilities  
<br />

---
**Next Post**: [[Basics]Understanding .NET Process Architecture: Modules, AppDomains, and Assemblies](https://dongheon3313.github.io/dotnet/Understanding-.NET-Process-Architecture-Modules,-AppDomains,-and-Assemblies/)
