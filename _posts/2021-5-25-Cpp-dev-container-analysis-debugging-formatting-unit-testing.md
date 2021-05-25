---
layout: post
title: C++ dev container static code analysis, debugging, formatting and unit testing
---

At this point I went in guns blazing and wrote pretty much every integration by hand. This is something to address down the road. There are extensions that are meant to make life easier and I did not make great use of them.

Among the goals were such diverse elements as static code validation, debugging, formatting, unit testing and a fanatical devotion to the pope - wait - that is a different scatch.

# static code validation

## cppcheck
I tested a few known cppcheck errors things were straight forward. cppcheck was already installed.

```cppcheck --relative-paths --template=gcc --language=c++ --enable=all --suppress=missingIncludeSystem --std=c++20 --force -ibuild .```

The ```problemMatcher``` was simply ```$gcc``` with a slight twist of setting the owner so messages are reset properly.

```
            "problemMatcher": { 
                "base": "$gcc",
                "owner": "cppcheck"
            }
```
## clang-tidy
clang-tidy worked pretty much the same way in terms of setting up the task and the ```problemMatcher```. 

This is the limited use command I selected to run:

```clang-tidy -checks=*,-llvmlibc-restrict-system-libc-headers,-fuchsia-trailing-return,-llvmlibc-* -p=build src/main/main.cpp -header-filter=.*, -fix``` - note that I am targetting the main file. I need to test this approach on a larger project. For now some messages are displayed and that was the low bar I was shooting for here.

Installing clang was a medium task and along with some other tools I did end up extending the docker file.

# debugging

debugging is remarkebly easy using the C/C++ extension. It is basically the default launch process and requires no modification. The only thing to watch out for is that the project is built with the debug flag in mind.

```"cmake .. -G \"Unix Makefiles\" -DCMAKE_BUILD_TYPE=Debug"``` I ended up setting three launch configurations for my demo project. One for main, and one for each testing framework (boost test / gtest).

# formatting

Using clang format requires processing one file at a time since applying fixes on multiple files at once can eaily backfire.
So I ended up iterating through the whole project and processing all .cpp and .h files one at a time. I am pretty sure this will kill my compile time in larger projects and is something I will definitely revisit.

Here is the task command I ended up using:

```
        {
            "label": "clang-format-llvm",
            "group": "build",
            "type": "shell",
            "command": "find src \\( -name '*.cpp' -o -name '*.h' \\) -exec clang-format -i -style=LLVM '{}' \\;",
        },
```

# unit testing

Some tings were easy some required changes in the setup.


Adding the dependency is as easy as adding two lines to the conanfile.

```
boost/1.76.0
gtest/1.10.0
```

compileing two stand alone executeables for these tests was also very easy as long as they did not import anything external.

Importing parts of the project required a cleaner cmake structure. I realize that there are some more things I need to do here and so I wont go into my current setup too much. Basically the root project contains four individual projects. Main and the two test projects are executeables and then there is one library core project that is imported by all three others. This is how I used to set up my projects in the past and this time around I found a lot of online references suggesting the same thing.

The ```problemMatcher``` implementation is not very satisfying and I thing some kind of test explorer plugin would go a long way to help here.

I ran with these hotfix problem matchers that seem to do the trick for now.

```
        {
            "label": "boost_test",
            "type": "shell",
            "command": "./build/bin/boost_test",
            "problemMatcher": {
                "owner": "boost_test",
                "fileLocation": [
                    "relative",
                    "/"
                ],
                "pattern": {
                    "regexp": "^([^(]+?)\\((\\d+)\\):[^:]+:[^:]+:(.+)$",
                    "file": 1,
                    "line": 2,
                    "message": 3
                }
            }
        },
```

```
        {
            "label": "google_test",
            "type": "shell",
            "command": "./build/bin/google_test",
            "problemMatcher": {
                "owner": "google_test",
                "fileLocation": [
                    "relative",
                    "/"
                ],
                "pattern": {
                    "regexp": "^(.workspaces.+?):(\\d+):\\s+([^[]+)$",
                    "file": 1,
                    "line": 2,
                    "message": 3
                }
            }
        },
```

# changed dockerfile

```
FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN sudo apt-get -y install software-properties-common
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys CC86BB64
RUN add-apt-repository ppa:ubuntu-toolchain-r/test
RUN add-apt-repository -y ppa:rmescandon/yq
RUN apt-get update
RUN apt-get -y install build-essential cmake cppcheck valgrind clang lldb llvm gdb python3-pip gcc-11 g++-11 yq jq clang-format-11 clang-tidy-11
RUN apt-get autoremove -y
RUN apt-get clean -y
RUN rm -rf /var/lib/apt/lists/*
RUN update-alternatives --install /usr/bin/clang-tidy clang-tidy /usr/bin/clang-tidy-11 10
RUN update-alternatives --install /usr/bin/clang-format clang-format /usr/bin/clang-format-11 10
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 10
RUN update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 10
RUN pip3 install conan
ENV CONAN_V2_MODE=1
USER vscode
RUN conan config init
RUN yq w -i ~/.conan/settings.yml 'compiler.gcc.version[+]' '11'
```

# what is next
I am not sure but I think I will look into style guides for project layout and common plugins next. 
I feel like there is some benefit to be had.
I will also look into the standard layout produced by CLion and see if that CMake Project could be made mostly compatible with what I am running here.

# a list of things I read or watched
[cppcheck homepage](http://cppcheck.sourceforge.net/), 
[cppcheck manual](http://cppcheck.sourceforge.net/manual.pdf), 
[clang format blog](http://www.mycpu.org/clang-format/), 
[clang format styles 1](https://bcain-llvm.readthedocs.io/projects/clang/en/release_37/ClangFormatStyleOptions/), 
[clang format](https://clang.llvm.org/docs/ClangFormat.html), 
[clang tidy](https://clang.llvm.org/extra/clang-tidy/), 
[clang tidy fuchsia](https://clang.llvm.org/extra/clang-tidy/checks/fuchsia-trailing-return.html), 
[clang tidy namespace](https://clang.llvm.org/extra/clang-tidy/checks/llvmlibc-implementation-in-namespace.html), 
[modern cmake](https://cliutils.gitlab.io/modern-cmake/), 
[vscode debug](https://code.visualstudio.com/docs/cpp/cpp-debug), 
[vscode launch](https://code.visualstudio.com/docs/cpp/launch-json-reference), 
[vscode tasks](https://code.visualstudio.com/docs/editor/tasks), 
[clang format all](https://github.com/eklitzke/clang-format-all/blob/master/clang-format-all), 
[cmake speed](https://gitlab.kitware.com/cmake/cmake/-/issues/21092), 
[core guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines), 
[clang llvm sytem lib header restriction](https://releases.llvm.org/11.0.0/tools/clang/tools/extra/docs/clang-tidy/checks/llvmlibc-restrict-system-libc-headers.html), 
[clang install](https://stackoverflow.com/questions/20756924/how-can-i-install-clang-format-in-ubuntu), 
[clang example](https://stackoverflow.com/questions/28896909/how-to-call-clang-format-over-a-cpp-project-folder), 
[cmake linking](https://stackoverflow.com/questions/29634101/cmake-how-to-define-target-without-linking-compilation-only), 
[vscode problem matchers](https://stackoverflow.com/questions/39673485/how-do-i-set-up-visual-studio-code-problemmatcher-to-match-c-errors), 
[cmake project setup 1](https://stackoverflow.com/questions/41814426/cmake-cant-find-header-files/41816651#41816651), 
[cmake project setup 2](https://stackoverflow.com/questions/43862273/cmake-c-include-relative-to-base-directory), 
[cppcheck in cmake](https://stackoverflow.com/questions/48625499/cppcheck-support-in-cmake), 
[cppcheck problem matcher](https://stackoverflow.com/questions/54541148/how-to-define-a-problem-matcher-for-cppcheck-task-in-vscode), 
[clang llvm sytem lib header restriction fix](https://stackoverflow.com/questions/65451147/how-to-fix-llvmlibc-restrict-system-libc-headers-check-in-clang-tidy), 
[cmake project setup 3](https://stackoverflow.com/questions/6986033/cppcheck-cant-find-include-files), 
[cmake debug / release](https://stackoverflow.com/questions/7724569/debug-vs-release-in-cmake), 
[clang format styles 2](https://www.clangpowertools.com/blog/getting-started-with-clang-format-style-options.html), 
[clang tidy example](https://www.reddit.com/r/cpp/comments/7obg9p/how_do_you_use_clangtidy/), 
[style guides](https://www.reddit.com/r/cpp/comments/8oja7y/which_c_style_guide_do_you_follow/), 
[Jason Turner clang-tidy](https://www.youtube.com/watch?v=OchPaGEH4TE), 