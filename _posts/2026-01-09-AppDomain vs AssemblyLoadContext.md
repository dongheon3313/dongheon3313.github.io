---
title: "[Basics]AppDomain vs AssemblyLoadContext"
categories: dotnet
tags: [Basic, CSharp]
layout: single
share: false
---

## Introduction

For a long time, **AppDomain** was the cornerstone of isolation in the .NET Framework.  
With the arrival of **.NET Core / .NET (5+)**, AppDomain was effectively deprecated for isolation purposes and replaced by **AssemblyLoadContext (ALC)**.

This change was not accidental. It reflects a **fundamental shift in CLR design philosophy**:
from *implicit, magical isolation* to *explicit, developer-controlled boundaries*.

This post explains **why AppDomain existed, why it failed, and why ALC is the modern replacement**.

---

## AppDomain in .NET Framework

### What AppDomain Was Designed For

AppDomain was introduced to provide:

- Logical isolation inside a single process
- Independent assembly loading
- Security sandboxing (CAS)
- The ability to unload assemblies

Conceptually:

```
One Process
 ├─ AppDomain A
 └─ AppDomain B
```

Each AppDomain appeared to be its own mini-runtime.

---

### How Cross-AppDomain Communication Worked

Objects could cross AppDomain boundaries in two ways:

1. **Marshal-by-value**
   - Objects marked with `[Serializable]`
   - Entire object graph copied

2. **Marshal-by-reference**
   - Objects inheriting from `MarshalByRefObject`
   - Accessed through a **Transparent Proxy**

```
AppDomain A
 └─ Transparent Proxy

AppDomain B
 └─ Real Object
```

The proxy intercepted method calls and forwarded them to the target AppDomain via .NET Remoting.

---

### Why AppDomain Became a Problem

Despite its promise, AppDomain had serious flaws:

#### 1. Hidden Runtime Costs
- Cross-domain calls looked like normal method calls
- In reality, they involved serialization and remoting
- Performance costs were invisible and unpredictable

#### 2. Fragile Unloading
- Static fields
- Event handlers
- Threads
- COM objects
- Native DLLs

Any of these could prevent `AppDomain.Unload()` from succeeding.

#### 3. Illusion of Isolation
AppDomain never truly isolated:

- Native libraries (process-wide)
- ThreadPool (process-wide)
- Static state leaks
- COM and Win32 resources

Isolation was **partial and leaky**.

#### 4. Security Model Collapse
- Code Access Security (CAS) was deprecated
- Sandboxing became irrelevant

---

## AssemblyLoadContext in .NET Core / .NET

### What ALC Actually Is

AssemblyLoadContext is **not a replacement for AppDomain** in terms of object isolation.

Instead, ALC provides:

- Assembly loading isolation
- Dependency resolution control
- Optional unloadability

Key idea:

> **ALC isolates assemblies, not objects.**

```
One Process
 ├─ Default ALC
 └─ Custom ALC (Plugin)
```

---

### No Proxies, No Remoting

Unlike AppDomain:

- No Transparent Proxy
- No implicit remoting
- No automatic cross-boundary calls

Objects created in an ALC are **real objects**, not proxies.

If two ALCs load the same type name:
- They are treated as **different types**
- Casting fails unless a shared contract is used

---

### The Contract-Based Model

The only supported way to communicate across ALC boundaries is through **shared assemblies**:

```
Default ALC
 └─ IPlugin.dll

Custom ALC
 └─ Plugin.dll → references IPlugin.dll
```

This enforces:

- Explicit interfaces
- Clear ownership
- Predictable behavior

There is no magic — only contracts.

---

### Why ALC Is More Honest

ALC embraces reality:

- Native code is process-wide
- Static state is shared
- Threads are shared

Instead of pretending to isolate everything, ALC:

- Makes boundaries explicit
- Forces developers to design for them
- Eliminates hidden runtime behavior

---

## Key Differences at a Glance

| Aspect | AppDomain (.NET Framework) | AssemblyLoadContext (.NET) |
|------|---------------------------|----------------------------|
| Object isolation | Partial (via proxy) | None |
| Proxy / remoting | Transparent Proxy | Not supported |
| Assembly isolation | Yes | Yes |
| Unload support | Fragile | Reliable (if designed correctly) |
| Native isolation | No | No |
| Security sandbox | CAS (deprecated) | Not supported |
| Design philosophy | Implicit magic | Explicit contracts |

---

## Why Microsoft Moved On

Microsoft removed AppDomain isolation because:

- It was complex and error-prone
- It hid performance costs
- It created false expectations of safety
- It was incompatible with modern runtime goals

ALC aligns better with:

- High-performance JIT (Tiered JIT, PGO)
- Modern hosting models
- Plugin architectures
- Long-running processes

---

## Conclusion

AppDomain tried to make isolation **easy and invisible** — and failed.

AssemblyLoadContext makes isolation **explicit and intentional**.

> **ALC does not protect you from bad architecture.  
> It forces you to design good architecture.**

That is why it survived — and AppDomain did not.
