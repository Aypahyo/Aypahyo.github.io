<a href="http://cppcheck.sourceforge.net/" title="CppCheck project page" target="_blank">CppCheck</a> 
is an easy to use static code analysis tool. 
If you want to use it right away, there is a stand alone GUI version in 
<a href="http://cppcheck.sourceforge.net/" title="CppCheck project page" target="_blank">the package</a>
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

<code>  &lt;Target Name="Build" Returns="@(ManagedTargetPath)"&gt;
    &lt;MSBuild Projects="..\Utility\cppcheck\cppcheck.vcxproj" properties="ProjectDir = $(ProjectDir)" /&gt;
    &lt;MSBuild Projects="$(MSBuildProjectFile)" Targets="NormalBuild" /&gt;
  &lt;/Target&gt;

  &lt;Target Name="NormalBuild" Condition=" '$(_InvalidConfigurationWarning)' != 'true' " DependsOnTargets="_DetermineManagedStateFromCL;$(BuildDependsOn)" Returns="@(ManagedTargetPath)"&gt;
    &lt;ItemGroup&gt;
      &lt;ManagedTargetPath Include="$(TargetPath)" Condition="'$(ManagedAssembly)' == 'true'" /&gt;
    &lt;/ItemGroup&gt;
  &lt;/Target&gt;</code>

There is a problem here that I do not adress. 
 The tests are not automatically excluded. 
 You may or may not want this though. 
 Since a copy of CppCheck will live inside of your project I suggest editing the cppcheck.vxproj file and just filter the files you want to exclude.


<code>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;Project DefaultTargets="CppCheck" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003"&gt;
    &lt;ItemGroup&gt;
        &lt;CppCheckFiles Include="$(ProjectDir)/**/*.c" /&gt;
        &lt;CppCheckFiles Include="$(ProjectDir)/**/*.cpp" /&gt;
    &lt;/ItemGroup&gt;   
 
    &lt;Target Name="CppCheck"
        Inputs="@(CppCheckFiles)" 
        Outputs="$(ProjectDir)\CCPCheck\%(CppCheckFiles.Filename)%(CppCheckFiles.Extension).stamp" &gt;
        &lt;Exec Command="cppcheck.exe %(CppCheckFiles.FullPath)  --enable=style --template=&quot;{file}({line}): error:{severity}-{id}: {message}&quot;"/&gt;
        &lt;MakeDir Directories="CCPCheck"/&gt;
            &lt;Touch 
                Files="CCPCheck\%(CppCheckFiles.Filename)%(CppCheckFiles.Extension).stamp" 
                AlwaysCreate = "true" /&gt;
    &lt;/Target&gt;
&lt;/Project&gt;</code>


The above xml code is the MSBuild ready buildscript for CcpCheck. 
I will go through it line by line and summarize some features.

<ul>
	<li><strong>line 2</strong></li> The default target is set so you do not have to worry about the implementation when calling it.
	<li><strong>lines 3 to 6</strong></li> The itemgroup of files you want to have checked. 
If you want to filter some files you can not filter them here. 
Removing files from an itemgroup is only possible once the task is running. I suggest creating a target dependency for that.
	<li><strong>line 8</strong></li> The inputs and outputs are important for the incremental build. 
It's all about the file timestamps. 
If the modification of an input file is younger than the last output file a rebuild is needed. 
Inputs and outputs have to map 1:1 for this to work.
	<li><strong>line 9</strong></li> The execution command starts CppCheck with parameters that are docummented in the CppCheck manual. 
The "--template" option provides the format for the output. 
All output is formatted to look like an error. 
The file/line combination in the output allows you to doubleclick the message in VS2010 and jump right to the error source.
	<li><strong>line 10</strong></li> This ensures that our timestamp directory exists.
	<li><strong>lines 11 to 12</strong></li> "Touch" and "always create" will create the timestamps that we need for incremental build to work.
</ul>

I hope this little guide is fun and useful for you. It is not all too detailed but it touches on enough detail to get anyone started.

Feel free to comment about your experience with this approach.