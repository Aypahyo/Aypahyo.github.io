---
layout: post
title: Developing C++ inside a dev container using vscode.
---

At the moment I am working on an ubuntu machine with vscode installed. There is a new thing out there where your IDE can run a container that features all dev tools you want so that when you move from machine to machine you can take your dev envirnment and all the tools with you. 

I want to find out how to do that for C++ so here is writeup while I do that. Step one is finding someone who did this already - you can find sites I read linked below.

# Here are the steps I followed:

 1. Get a coffee while remembering that I have all prerequisits.
 docker and vscode already installed. (docker, vscode, plugin for docker, plugin for Remote - Containers)
 2. Clone the demo repo [git@github.com:microsoft/vscode-remote-try-cpp.git](git@github.com:microsoft/vscode-remote-try-cpp.git)
 3. navigat to the folder and open it  ```vscode .```
 4. click the lower left gren dev container icon and select ```Remote-Containers: Open Folder in Container```
 5. click the run and debug button on the left.
 6. profit

# Summary

Especially when you have your basic tools installed. Starting up a project that was checkd in like the c++ sample is a breeze.
Checking in the the .devcontainer and the .vscode folders seems imperative in order to get a smooth experience.

Introducing an error like ```1++;``` shows the appropriate compiler warning in the Problem section of vscode.

Autocompletion works for c++ keywords like ```double```, for functions in the same file, for standard libraries and that is how far my testing went for now.

cmake and python 2.7.16 are installed in the container.

# Things I will look into later.

Introducing a core community guideline problem like ```int arr[10];``` does not show up as an issue.

Setting up conan (pip and conan are not installed).

Setting up testing.






Things I read while reseaching the topic:
 * [Developing inside a Container](https://code.visualstudio.com/docs/remote/containers)
 * [#VSCode: C++ Development and Debugging using containers](https://lemariva.com/blog/2020/10/vscode-c-development-and-debugging-containers)
 * [Using VS Code for C++ development with containers](https://devblogs.microsoft.com/cppblog/using-vs-code-for-c-development-with-containers/)
 * [VS Code Remote / GitHub Codespaces Container Definitions](https://github.com/microsoft/vscode-dev-containers)
 * [Try Out Development Containers: C++](https://github.com/microsoft/vscode-remote-try-cpp)