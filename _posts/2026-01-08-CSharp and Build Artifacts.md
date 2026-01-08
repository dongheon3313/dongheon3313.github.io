---
title: "[Basics]Understanding C# Build Outputs"
categories: dotnet
tags: [Basic, CSharp]
---

## C# and Build Artifacts

When we write code and go through the build process, the following flow takes place.

```text
C# source (.cs)
  -> csc (C# compiler executable)
  -> IL (e.g., IL_0000: ...) + metadata
  -> .dll / .exe
```

In practice, Visual Studio—the tool we commonly use—is an editor, debugger, and UI shell.  
When you click **Build** for a C# project in Visual Studio, the actual compilation is performed by an executable included in a separate SDK, as shown below.

```text
Visual Studio
 |- Editor, debugger, UI
 |- Internally invokes csc.exe
        ^ included in the .NET SDK
```

The DLLs and EXEs produced in this way share the following common characteristics:

- Files that contain *executable code*
- Use the PE (Portable Executable) format (on Windows)
- Exist on disk and are later loaded into memory
- Can contain native code or IL (.NET) code  
  (*Native code is machine code; IL is an intermediate language that the CLR translates into machine code*)
- Are loaded into a process’s address space and participate in execution

However, an EXE serves as the entry point that boots the CLR, whereas a DLL is an assembly that the CLR loads and executes.

Put differently:

- **EXE**: A file that can start a process  
- **DLL**: A file that provides functionality within an already running process

The fundamental difference between an EXE and a DLL lies not in the *file format*, but in *whether it can start a process or not*.
