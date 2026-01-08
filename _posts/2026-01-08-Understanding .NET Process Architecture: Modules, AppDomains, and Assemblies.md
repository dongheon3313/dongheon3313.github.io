---
title: "[Basics]Understanding .NET Process Architecture: Modules, AppDomains, and Assemblies"
categories: dotnet
tags: [Basic, CSharp]
---

## Key Terminology

To clarify these concepts, let's review the explanation of each term below.

**Process**: A single running program

**Module**: A 'file unit' loaded into memory by a process

**CLR (Common Language Runtime)**: The runtime engine that exists as actual DLL files (clr.dll, coreclr.dll). From the OS perspective, it's just a DLL. From the process perspective, it's a loaded 'module'.

**AppDomain (Application Domain)**: A logical space where the .NET runtime (CLR) manages code

**Assembly**: A 'unit of code bundle' recognized by .NET and managed by the CLR

## Architecture Hierarchy

```
[ Process (revit.exe) ]
    ├─ Module: revit.exe
    ├─ Module: user32.dll
    ├─ Module: clr.dll
    │
    └─ AppDomain
         ├─ Assembly: RevitAPI.dll
         ├─ Assembly: MyAddin.dll
         └─ Assembly: Newtonsoft.Json.dll
```

## CLR Loading Rules

In the case of .NET processes, **only one CLR is loaded per process**.

Therefore, the relationship is: **[Process : CLR] = [1 : 0] or [1 : 1]**

### Key Points

- **A single process can have at most one CLR**, and when the CLR is loaded, it has one or more AppDomains.

- **Resource management of AppDomains is entirely handled by clr.dll**, and the OS only considers clr.dll as the target for resource management.
