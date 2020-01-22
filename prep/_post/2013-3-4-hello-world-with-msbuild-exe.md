---
layout: post
title: hello world with msbuild.exe
---


I recently sorted my old code and i was thinking that i will not keep all of it.  While i delete most of it though i will remake some of it and put it up on this blog.

If i start a new project these days i usually select my toolchain first.  Afterwards i copy all tools i want to use somewhere into my project.  That way i dont have to worry about their versions later.  The final step before starting to code is to mix all tools into the build process. That is quite important since it allows you to do all you want to do in one click.

Along this line i will present a mini series of MSBuild tutorials to show how i link the programs.  

Starting, as alway, with a hello world example.

```xml
<xml version="1.0" encoding="utf-8">
<Project DefaultTargets="mainTarget" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
	<Target Name="mainTarget">
	  <Message Text="Hello World" Importance = "high">
	</Target>
</Project>
```


This code can be saved in any file and msbuild.exe will take the filename as a parameter.  There is a DefaultTargets information that tells MSBuild what to execute if no target is specified. The target has a task that will print a message on execution. The message has its importance increased to maximum, so it gets displayed without regard to a verbosity level. I saved it on my computer as a simple txt file. After locating the highest version of MSBuild.exe on my computer i executed the xml script.

The following output was created on my machine
(german, because the world hates me and defaults to installing everything in german for me):

![alt]("http://blog.aypahyo.net/pictures/MSBuild_HelloWorld_Output.png")


Microsoft maintains a [MSBuild.exe](http://msdn.microsoft.com/en-us/library/0k6kkbsd(v=vs.100).aspx) reference.

1. The things you want MSBuild.exe to do are grouped as targets.
2. Targets consist of tasks.
3. There is other stuff as well.


The capabilities of MSBuild appear pretty much complete.
Microsoft delivers a set of [Tasks](http://msdn.microsoft.com/en-us/library/7z253716(v=vs.100).aspx)
that can be used to create Targets. 
There are more than the microsoft tasks available. 
Everyone can write [new tasks in C#](http://lmgtfy.com/?q=How+to+create+custom+msbuild+task)
Even better, most are already available and collected in other sources online. 
Examples include [the ZIP task or the nunit (c#) task](http://msbuildtasks.tigris.org/)


A new C++ project comes with a premade *.vcxproj file. 
This fresh file contains references to other prepared msbuild files.
The actual build targets are somewhat hidden in the referenced files.

Portions of the generated project file are managed by visual stuio and are best left alone. 
Some portions can be overridden to change behaviour. 
Some portions are best modified from within visual studio because they have to line up with changes to the solution file. 
Some portions are waiting to be added by the customizing programmer (procustogrammer).

In the upcoming days i will link a cpp toolchain together. 
I will blog about it here for all readers including future me.