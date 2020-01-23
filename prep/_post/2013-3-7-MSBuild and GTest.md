---
layout: post
title: MSBuild and GTest
---

Noting your intentions in testform is an excellent idea.
To do that you want to have a testframework like
[googletest](https://code.google.com/p/googletest/downloads/list).

After getting a testframework you want to set it up so that it runs every time you press on compile.
In fact you most likely do not want to run the program at all but just run your tests.
As long as you do not break a test your code is still what you intended it to be.
If you find a bug, just add a test.
If you want to add a feature add a test for it first.

Most buildcontrol tools can be set up so that one click compilation and test will work.
Here is one way to do it with MSBuild.

Create a new empty C++ project without any fancy stuff like precompiled headers etc.
In that project you want two new files.
One `something_main.cpp` and one `something_test.cpp`.

Specifically the endings are important.
The endings will be used to filter your files later.
For example we avoid a conflict between two main functions.
One from the testframework, one from your application.
Another useful thing is to filter other test from unittests.
That way you could have a seperate build for your time consuming tests.

You can not copy paste the following files easily, some manual work is required.
Here is the content of the files:

`something_test.cpp`

```cpp
#include "../Utility/GTest/gtest.h"
TEST(casename, testname){ EXPECT_EQ(1,1); }
```

`something_main.cpp`

```cpp
void main(void){}
```

[something.vcxproj](http://blog.aypahyo.net/files/MSBuild_GTest_XML.vcxproj)

Most of `something.vcxproj`, the build file, is automatically generated with just a small portion of manual editing.
After you created a new project the vcxproj file already exists.
At some point you want to copy the testframework into your project, or at least that is what i do.
The place where you want it to be already influences some lines in the other files.
You need the path for all include statements.
The include statement of `something_test.cpp` and
the include statement of the build xml (line 153).
Your overall foldersetup will look something like the folders in the next picture.
Note how few files the googletest framework will use.
Also I screwed up the naming - but do not worry about that.

![alt](http://blog.aypahyo.net/pictures/MSBuild_GTest_Folders.png)

The xml build contains references to a test build configuration.
To get it you would have to edit the xml file and the solution file.
I suggest taking the shortcut via Visual Studio.
Use the
[assistant](http://msdn.microsoft.com/en-us/library/kwybya3w.aspx)
to create a new build configuration.
Copy the Properties for it from the Debug configuration.
And Name it "Test" - that is the name used in the build xml above.
If you can not access the assistant you most likely have the express edition installed.
In that case you have to
[confgure visual studio first](https://link)
http://www.gregcons.com/KateBlog/HowDoIMakeADebugBuild.aspx

[greyed out configuration manager](http://www.gregcons.com/KateBlog/HowDoIMakeADebugBuild.aspx)
first.

Posting the xml build file does not look good and takes up a lot of space.
However talking about it without the file is kind of pointless.
So please go ahead and [Demofile](http://blog.aypahyo.net/files/MSBuild_GTest_XML.vcxproj).
I suggest opening it with [notepad++ project home](http://notepad-plus-plus.org/) and switching the language to xml.
Reading from top to bottom through the build xml:

- *line 80* Here are the precompiler definitions for the Test|Win32 build.
The _Debug definition was removed since it can interfere with the testframework.
An empty property $(INJECT_HERE) was inserted.
This allows us to inject preprocessor definitions later.
- *lines 104 to 113* Leave this part alone.
Visual Studio will automanage this part.
It basically lists all your project files that are to be compiled.
- *line 115 to 117* Another inject mechanism.
This one creates a key.
If we start in test configuration the key contains a long string.
If we start in non test configuration the key contains an empty string.
Looking at this key later will tell us if we started in test configuration.
- *line 122* The default build target gets re-specified here.
This overrides the old buildtarget.
The old buildtarget was set during one of the import statements.
If you want to take a look at them you can output their path using the message task.
They are also xml files following the msbuild schema.
- *line 123* Deleting the gtest obj files ensures a rebuild of them (unless nothing at all was edited)
That way precompiler definitions are reapplied.
- *line 124* in any case and for all builds, the target TestBuild is executed.
Testbuild is specified below.
It basically builds the unittests.
Since testbuild is the only target in Test configuration this will return the testexecuteable to Visual Studio.
Visual Studio will then execute the returned executeable if you started the build as build and run.
- *line 125 and 126* These two lines are conditional and are executed when not executing test.
RunTest runs the tests and outputs to the buildlog.
If the tests fail the returnvalue of the executed gtest file tells msbuild to abort the build.
There will be a builderror in Visual Studio allowing you to see what went wrong.
- *line 136* this is my favorite line in the whole file.
There is an inject to inject a keyword into the preprocessor (see line 80)
But only if it is run in testmode the injected string is not empty. (see line 116)
This allows your testframework wheather it was started with the testconfiguration or not.
Provided of course your testframework gets recompiled at every run. (see line 123)
- *line 139 to 144* this is a copy of the original build from one of the imported files.
the new build did overwrite it - but since it is still needet here is a copy of it.
The only change is the new dependencie ModifyFiles.
- *line 146* Just a simple exec task to run the tests.
- *lines 150 to 157*This target filters the files before the normal build is executed.
For the testbuild the main file has to be removed since the testframework has its own main function.
Also we want to remove all testcode from the normal release and debug builds
This is the place to filter if you want a _fake implementation for every class or _integration for non unittests.

When I am testing I like my console to remain open so I can enjoy the view.
Hitting CTRL + F5 works here, but usually I am too lazy to hit that correctly.
After jumping through a lot of hoops to get the precompilerdefinition through,
we are free to add a conditional pause to the googletest main function.
Something along these lines:

```cpp
#ifdef MSBUILD_RUN_WITH_STOP_AT_END
std::cout<<"....\n";
_getch();
#endif
```

You should be able to copy these results and have your own googletest build party.
When you do, please pay attention to the colors of googletest when executing the test build.
The regular wondows console is not an ansi console
and I do not have colors there using ansi escape sequences.
However if you run the tests within the Visual Studio console there are colors.
That makes me wonder if Visual Studio ships with its own ansi console. If you have information about that feel free to comment.

There are some things, like incremental build or creating config files on the fly, that I was unable to explain in this context. I will continue in the next few days with CppCheck and/or PClint to fill that gap.
