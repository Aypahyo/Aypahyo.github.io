---
layout: post
title: tic tac toe refurbished
---

While moving my blog I noticed that my tic tac toe files are not ready for my GitHub blog.
It is funny to me how I come back to this project every couple of years to rebuild it with new knowledge.

Some immediate things that I noticed in my old project are some setup thing.
I used to link libraries by hand instead of using [nuget](https://www.nuget.org/).
My mocking framework was [rhinomocks](https://www.nuget.org/packages/rhinomocks/) which is no longer supported.
[rhinomocks](https://www.nuget.org/packages/rhinomocks/) also has no support for [APS.NET](https://dotnet.microsoft.com/apps/aspnet) which makes [moq](https://www.nuget.org/packages/Moq/) a better choice.

I also have some new skills lined up that I want to aquire so maybe I will tinker with tic tac toe for a bit.

For the 2020 Version I will go for a c++ core with a CLR translation to C# and a WPF frontend.
Some extension goals that I am thinking about are:

- [NodeJS](https://nodejs.org/en/) frontend
- [CMake](https://cmake.org/) in my build chain
- building and releasing using github or cloud tech
- online multiplayer
- making it into a [steam](https://store.steampowered.com/) game.

I am going to start with a simple project setup and work my way up fro there.
The general idea is to copy most stuff from my old project while also working in small iterations, I am not quite sure how that will play out.

I have a rough Idea how I want the packages to look like so here is how I will be working on the project:

![packages](images/2019-2-2/packages.png)
