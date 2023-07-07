# dotnet-native-aot

Feature that produces an application that is pre-compiled to native code (down to single binary), allowing run on the machine without installed .NET runtime. This is in contrast with Just-in-Time compilation, where the IL code is compiled into native code at runtime. Compilation step with Native AOT occurs ahead of a time, typically during the build process or as a separate pre-compilation step.
Feature went live with .NET 7 and currently is in preview for ASP.NET Core 8.0.


## Getting started

To use .NET 7 Native AOT be sure to follow these general steps:
1.	We need to set the TargetFramework to .Net 7.0
2.	Because Native AOT uses Lambda custom runtimes (allow us to bring own runtime to Lambda) we need to set up bootrsap file which Lambda service will look for. And also, we need to set flag PublishAot to true. 

**Updates required to csproj file:**


`<TargetFramework>net7.0</TargetFramework>`

`<AssemblyName>bootstrap</AssemblyName>`

`<PublishAot>true</PublishAot>`


3.	Build our project using the appropriate build configuration that includes Native AOT compilation which will generate native binaries.
4.	Test and validate the generated binaries to ensure proper functionality and performance.
5.	Deploy the native binaries along with any required dependencies to the target environment.
For deploying use this command:

`dotnet lambda deploy-function`

## What are the benefits of .NET Native AOT

Some of the benefits are:    

+	**Improved Start-up performance** – no need for JIT compilation during application startup, which is beneficial in environments with multi-deployed instances (like AWS Lambda). 

+	**Reduced Memory Footprint** – generates machine code that is specific to target architecture, which can lead to optimized memory usage.

+	**Protection of Intellectual Property** – compiled native binaries is harder for potential attackers to reverse-engineer our code, which can help to protect intellectual property and sensitive algorithms. 

+	**Platform Independence** – providing greater portability with allowing us to distribute applications as a standalone executable without requiring a specific version of the .NET runtime to be installed on the target machine. This eliminates potential compatibility issues also. 

**Limitation**:   
+	Must compiled on the OS and processor architecture that will be in production

+	Lack of support for run-time code generation meaning that any used JSON (de)serialization (System.Text.Json and Newtonsoft.Json) libraries will require changes.

## What are the potential risks/costs of using .NET Native AOT


+	**Increased Deployment Size** – Compiling to native code results in larger deployment packages compared to JIT compilation, because it includes the .NET runtime components required to execute the application.

+	**Limited Platform Support** – Native AOT currently supports a subset of platforms compared to the full .NET runtime. It’s important to be aware of the supported platforms before deciding to use Native AOT.  
    1.	*Supported Architectures* – Supports x64 and Arm64, so be sure to check if target platform’s architecture is supported.  
    2.	*Operating system compatibility* – Supports popular platforms like Windows, macOS and Linux, but it’s essential to review the platform compatibility matrix.  
    3.	*Third-Party Library support* 

+	**Trade-offs in Debugging Experience** – Some debugging features may be limited or unavailable in the native environment. If extensive debugging capabilities are necessary it’s important to evaluate the impact on our debugging workflow and consider alternative approaches.


## Comparison between JIT and Native AOT

| | JIT| Native AOT
|:---|:---:|:---|
|Compilation Process|Compilation from IL to native code happens in runtime.|Compilation happens during the build process or as a separate step before deploy.|
|Startup Performance|The compilation time adds to the startup time, which can impact the perceived responsiveness of the application.|Since the code is already compiled, the application can start faster, resulting in more responsive user experience.|
|Memory usage|JIT compilation generates machine code dynamically, optimizing it for the specific execution environment. This results in efficient memory usage, but also means that code occupies memory during runtime.|More optimized memory usage by tailoring the generated native code to the target hardware, which lead to reduced memory footprint.|
|Debugging Experience|Developers can attach debuggers, set breakpoints, and dynamically modify the code during runtime.|More challenging compared to JIT-compiled code. Developers should consider the impact on their debugging workflow.|

Conclusion – For cases when fast application is critical and optimizing memory usage is a priority, Native AOT is better option. For some other cases, a hybrid approach can be better option. 

## Resources

+ 	[Native AOT | Serverless .NET (serverlessdotnet.dev)](https://serverlessdotnet.dev/docs/advanced/native-aot)
+ 	[Native AOT deployment overview - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/?tabs=net7)


## License

This project is licensed under the [MIT License](https://opensource.org/license/MIT) 
