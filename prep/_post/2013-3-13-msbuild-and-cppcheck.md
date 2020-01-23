---
layout: post
title: MSBuild and Cppcheck
---

[CppCheck](http://cppcheck.sourceforge.net/)
is an easy to use static code analysis tool.
If you want to use it right away, there is a stand alone GUI version in
[the package](http://cppcheck.sourceforge.net/)
.
Static code analysis is like having another programmer look over your code and comment on what you have written.
I think it is a good idea to run a lot of non conflicting static tools simultaneousely.
I even think that it is a good idea to hinder compilation if such a tool lists any errors.
CppCheck states on its project page "zero false positives" - so running it should never have a negative effect.

Here I want to show how to get MSBuild to execute a CppCheck task.
The following snippet is the one I want to write into my project xml.
It just calls the actual CppCheck target somewhere else.
The point of that is of course the attempt of getting some reuse out of the effort.

The examples in this text looks broken because the text highlighting tool used is breaking it.
For some reason it keeps creating endtags where I don't want them.
So copy with care and work yourself forward in small increments.

```xml
  <Target Name="Build" Returns="@(ManagedTargetPath)">
    <MSBuild Projects="..\Utility\cppcheck\cppcheck.vcxproj" properties="ProjectDir = $(ProjectDir)" />
    <MSBuild Projects="$(MSBuildProjectFile)" Targets="NormalBuild" />
  </Target>

  <Target Name="NormalBuild" Condition=" '$(_InvalidConfigurationWarning)' != 'true' " DependsOnTargets="_DetermineManagedStateFromCL;$(BuildDependsOn)" Returns="@(ManagedTargetPath)">
    <ItemGroup>
      <ManagedTargetPath Include="$(TargetPath)" Condition="'$(ManagedAssembly)' == 'true'" />
    </ItemGroup>
  </Target>
```

There is a problem here that I do not adress.
 The tests are not automatically excluded.
 You may or may not want this though.
 Since a copy of CppCheck will live inside of your project I suggest editing the cppcheck.vxproj file and just filter the files you want to exclude.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="CppCheck" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <CppCheckFiles Include="$(ProjectDir)/**/*.c" />
        <CppCheckFiles Include="$(ProjectDir)/**/*.cpp" />
    </ItemGroup>

    <Target Name="CppCheck"
        Inputs="@(CppCheckFiles)"
        Outputs="$(ProjectDir)\CCPCheck\%(CppCheckFiles.Filename)%(CppCheckFiles.Extension).stamp" >
        <Exec Command="cppcheck.exe %(CppCheckFiles.FullPath)  --enable=style --template=&quot;{file}({line}): error:{severity}-{id}: {message}&quot;"/>
        <MakeDir Directories="CCPCheck"/>
            <Touch
                Files="CCPCheck\%(CppCheckFiles.Filename)%(CppCheckFiles.Extension).stamp"
                AlwaysCreate = "true" />
    </Target>
</Project>
```

The above xml code is the MSBuild ready buildscript for CcpCheck.
I will go through it line by line and summarize some features.

- *line 2* The default target is set so you do not have to worry about the implementation when calling it.
- *lines 3 to 6* The itemgroup of files you want to have checked.
If you want to filter some files you can not filter them here.
Removing files from an itemgroup is only possible once the task is running. I suggest creating a target dependency for that.
- *line 8* The inputs and outputs are important for the incremental build.
It's all about the file timestamps.
If the modification of an input file is younger than the last output file a rebuild is needed.
Inputs and outputs have to map 1:1 for this to work.
- *line 9* The execution command starts CppCheck with parameters that are docummented in the CppCheck manual.
The "--template" option provides the format for the output.
All output is formatted to look like an error.
The file/line combination in the output allows you to doubleclick the message in VS2010 and jump right to the error source.
- *line 10* This ensures that our timestamp directory exists.
- *lines 11 to 12* "Touch" and "always create" will create the timestamps that we need for incremental build to work.

I hope this little guide is fun and useful for you. It is not all too detailed but it touches on enough detail to get anyone started.

Feel free to comment about your experience with this approach.
