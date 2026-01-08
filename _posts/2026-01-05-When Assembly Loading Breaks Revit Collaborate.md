---
title: "When Assembly Loading Breaks Revit Collaborate"
categories: revit-api
tags: [revit, collaborate, worksharing]
---

# Problem

After installing my Revit Add-in, **Collaborate / Worksharing initialization failed** with the following message:

> **Initiate Collaboration failed. Check the journal for more details**

Important observations:

* Same Revit version, same machine, same model
* **Collaborate works perfectly when the add-in is NOT loaded**
* **Collaborate fails consistently when the add-in IS loaded**

This immediately ruled out model corruption, licensing, or network issues.
The problem had to be **inside the add-in loading process itself**.

---

# Journal Clues

In the Revit journal, I repeatedly found these lines:

```
ExternalDBApplication Autodesk.Revit.UI.Collaborate.CollaborateDBApplication
was not loaded in RevitWorker

ExternalDBApplication AXMImporterWrapper.AXMImporterApp
was not loaded in RevitWorker
```

At first glance, this looks like a missing or broken Autodesk component.
In reality, **this log is a symptom, not the root cause**.

---

# My Original Add-in Architecture

My add-in was structured like this:

```
Revit
 └─ Interface / Loader DLL (A.dll)
     └─ OnStartup()
         ├─ Loads many dependent DLLs
         └─ Loads the actual add-in DLLs
```

The loader DLL dynamically loaded:

* ClosedXML / OpenXML related DLLs
* System.Memory / System.Buffers / Unsafe
* WebView2 assemblies
* Other third-party libraries
* The real add-in implementation DLL

**Important detail:**

> The versions of these packages were carefully matched to the versions Revit itself uses.
>
> **This was NOT a version conflict problem.**

---

# What Actually Went Wrong

The real issue was **not package versions**.

The issue was:

> **Too many assemblies were being loaded too early, during Revit startup.**

## Why this breaks Collaborate

* Revit starts multiple processes during Collaborate initialization

  * `Revit.exe` (UI process)
  * `RevitWorker.exe` (background process for collaboration)

* `OnStartup()` runs **before Revit reaches a stable state**

* My loader DLL aggressively loaded:

  * A large number of managed assemblies
  * Assemblies that themselves trigger further dependency resolution

This polluted the AppDomain and assembly resolution state **before** `RevitWorker` finished initializing its own internal components.

As a result:

* RevitWorker failed to initialize its `ExternalDBApplication`s
* Collaborate aborted with:

  > *Initiate Collaboration failed*

---

# Why Removing Some DLLs Broke the UI

I tried another experiment:

* Prevent loading most dependency DLLs
* Only load the "real" add-in DLL

Result:

* The add-in **did not appear in the Revit UI at all**

This is expected behavior:

* The real add-in DLL had **compile-time references** to those packages
* CLR resolves referenced assemblies **at load time**, not when code is executed
* Missing dependencies cause silent load failure

Revit does not show an error dialog — it simply ignores the add-in.

---

# Key Insight

> **The problem was not *what* assemblies were loaded, but *when* and *how many* were loaded.**

Even perfectly compatible packages can break Revit if:

* They are loaded during `OnStartup()`
* They introduce large dependency trees
* They alter assembly resolution too early

---

# Final Fix

I changed the architecture to follow a strict separation of responsibilities.

## ✔ What stays in the Revit Entry DLL

* Only Revit API references
* Ribbon / UI registration
* Lightweight `IExternalCommand` classes

No heavy third-party dependencies.

## ✔ What moves out of startup

* Heavy libraries (OpenXML, Excel, WebView2, etc.)
* Large dependency graphs

These are now:

* Loaded lazily **only when a command is executed**, or
* Executed in a **separate helper EXE** when appropriate

---

# Takeaway

> **In Revit Add-ins, assembly loading itself is an operation with side effects.**
>
> Even if versions are correct, loading too much — too early — can break core Revit features like Collaborate.

The fix was not about dependency versions, but about **respecting Revit’s startup and collaboration lifecycle**.

---

# Practical Rule of Thumb

* Keep the add-in entry DLL *extremely lightweight*
* Avoid loading large dependency trees in `OnStartup()`
* Treat assembly loading as part of your runtime logic, not initialization

This change made Collaborate fully stable again.
