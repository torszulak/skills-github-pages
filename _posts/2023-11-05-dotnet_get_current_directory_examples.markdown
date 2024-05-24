---
layout: post
title:  "Few ways to get current directory in .NET"
date:   2023-11-05
description: In .NET current directory path can be get in few ways. In this post I want to describe some of them and show how they work in specific cases.
seo_title: How to get current directory in .NET
seo_description: In .NET current directory path can be get in few ways. In this post I want to describe some of them and show how they work in specific cases.
excerpt: In .NET current directory path can be get in few ways. In this post I want to describe some of them and show how they work in specific cases.
---

<p class="intro"><span class="dropcap">S</span>ometimes in our application, we need to obtain the current directory path. The purposes for doing so can vary: we might want to find a specific file or even load an external assembly. To achieve this, we have to determine the current location of our running application. .NET provides us with a few ways to do this, but some of them can be tricky in specific cases.</p>

**Property AppContext.BaseDirectory**  
  
  In .NET this property returns path to directory that contains the entry point assembly (directory of the host executable). You could consider it like replacement for ``AppDomain.CurrentDomain.BaseDirectory`` from .NET Framework.

**Directory.GetCurrentDirectory()**

Returns the path from **where you start** the application. It won't always be the same path as ``AppContext.BaseDirectory``. For example if you run your .NET console application from Powershell in the directory where .exe file exists it returns:

{%- highlight powershell -%}
PS D:\Application\bin\Debug\net7.0> .\Application.exe
Directory.GetCurrentDirectory() returns: D:\Application\bin\Debug\net7.0
{%- endhighlight -%}

but if you run the same app from parent folder it will return:
{%- highlight powershell -%}
PS D:\Application> .\bin\Debug\net7.0\Application.exe
Directory.GetCurrentDirectory() returns: D:\Application
{%- endhighlight -%}

In the same way it will be work if you run your application which is hosted as Windows Service - then ``GetCurrentDirectory()`` method will return path to _services.exe_ file: Windows\system32. So you should be aware if you use this method 😉

Property ``Environment.CurrentDirectory`` works in exactly the same way as ``Directory.GetCurrentDirectory()`` method.


**Get location by reflection**  

You can also get current directory using reflection.  
.NET offers two similar method: ``Assembly.GetExecutingAssembly().Location`` and ``Assembly.GetEntryAssembly().Location``.  
The difference between them is that ``GetExecutingAssembly()`` returns assembly that contains the code that is currently executing, while ``GetEntryAssembly()`` returns assembly that is the process executable in the default application domain, or the first executable that was executed by ``ExecuteAssembly(String)``.  
For example if you run above methods in test project ``GetExecutingAssembly()`` will naturally return the test project assembly, but ``GetEntryAssembly()`` will return assembly of test runner (e.g. testhost.dll or ReSharperTestRunner.dll).  
The usage of ``Assembly.GetExecutingAssembly().Location`` could be tricky in some scenarios. Consider a situation where you have main application that, in some way, uses an external assembly (e.g. load some plugin) as follows:
{%- highlight csharp -%}
Assembly assembly = Assembly.LoadFrom($"{AppContext.BaseDirectory}\\plugins\\OtherProject.dll")
{%- endhighlight -%}
_Note that desired dll is located in application subdirectory 'plugins'_.  

If you call method from this assembly that uses ``GetExecutingAssembly()`` **it returns the path to 'plugins' folder, not to the application directory**.


### Summary
I presented you only few - in my opinion most popular - ways to obtain the current dictionary path. More importantly I would like to emphasize that seemingly simple code be confusing when debugging our running application. 😉

Code with examples is available on my [Github](https://github.com/torszulak/current-directory-example)