# External-Invocations
.NET assemblies used for invoking common executable tools in a separate process.

[![Build status](https://ci.appveyor.com/api/projects/status/ug9ioss7q2fjmxml?svg=true)](https://ci.appveyor.com/project/Boyko-Karadzhov/external-invocations)
*Tests are not being ran automatically because of special environmental requirements.*

## Features
- API for compiling code using the Visual Studio C/C++ Optimizing Compile;

## Requirements
- Visual Studio 2010 - 2013 (for VisualStudioCommonTools);
- [NVidia CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit) (for NvidiaToolkit);

## Disclaimer
- This project does not contain a compiler. It invokes the VC compiler that is present on the system where VisualStudioTools assembly is called.

## Installation

To install External-Invocations, run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)

    PM> Install-Package Karadzhov.ExternalInvocations

## Usage

### Invoking the VC compiler
1. Make sure your project references the **Karadzhov.ExternalInvocations.VisualStudioCommonTools** assembly;
1. Add a using statement for this namespace: **Karadzhov.ExternalInvocations.VisualStudioCommonTools**;
1. Create a new instance of the **COptimizingCompiler** class;
   - You can pass an argument to the constructor specifying the target platform. Amd64 and X86 are supported;
   - If you use the parameterless constructor the COptimizingCompiler class will target the architecture the current process is running in;
1. Create an instance of the **CCompileArguments** class;
   - Use the **Files** property to pass file paths to the compiler ([CL Filename Syntax](http://msdn.microsoft.com/en-us/library/9bk45h3w.aspx));
   - Choose level of optimization by setting the **Optimizations** property (available options are **None** and **MaximumOptimizations**);
   - Set **IsDll** to true if you want the compiler to produce a dynamic library;
   - The Output property to instruct the linker about the output file name ([/OUT (Output File Name)](http://msdn.microsoft.com/en-us/library/8htcy933.aspx));
1. Call **COptimizingCompiler.Compile** with the arguments you have created to invoke the compiler;
   - There is no return value. _Karadzhov.ExternalInvocations.VisualStudioCommonTools.CompilationException_ is thrown if the compilation was not successful.

### Sample
This sample compiles a C source file into a DLL using C#.

```CSharp
   var compiler = new COptimizingCompiler();
   var arguments = new CCompileArguments();
   arguments.Files.Add(Path.Combine(samplesPath, "sum.c"));
   arguments.Optimizations = CompileOptimization.MaximumOptimization;
   arguments.IsDll = true;
   arguments.Output = "bin\\sum.dll";

   compiler.Compile(arguments);
```

### Invoking the CUDA compiler
1. Make sure your project references the **Karadzhov.ExternalInvocations.NvidiaToolkit** assembly;
1. Add a using statement for this namespace: **Karadzhov.ExternalInvocations.NvidiaToolkit**;
1. Create a new instance of the **CudaCompiler** class;
1. Create a new instance of the **CudaCompileArguments** class;
   - This class inherits from _CCompileArguments_. Refer the instructions for the VC compiler for details;
   - Set the **ComputeCapability** property to specify the target [GPU compute capability](https://developer.nvidia.com/cuda-gpus);
   - Set the **TargetProcessorArchitecture** to specify whether 32 or 64 bit binary should be compiled;
1. Call **CudaCompiler.Compile** with the arguments you have created to invoke the compiler;
   - There is no return value. _Karadzhov.ExternalInvocations.VisualStudioCommonTools.CompilationException_ is thrown if the compilation was not successful.

## See also

Now that you are compiling DLLs on the fly you can also call them right away by using the Dynamic-Libraries project: [Dynamic-Libraries](https://github.com/Boyko-Karadzhov/Dynamic-Libraries)
