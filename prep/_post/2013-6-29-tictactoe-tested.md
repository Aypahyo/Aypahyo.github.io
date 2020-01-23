---
layout: post
title: tic tac toe tested
---

I recently got married and had a lot of other stuff do do as well. My apologies for not releasing a new post in a while.

Now I did not get around to try out *FX Cop* so that remains on my todo list. But I added some unit tests to my project.

I coded the TicTacToe the dirty way without test in my first iteration. There are a couple of downsides to it - the worst being that you release untested code to your customer - basically telling your customer that you did not ensure that you actually did the job he asked you to do.

I write this because during introducing tests I noticed a quite sever programming bug. The rules that check if a win condition was triggered had an error. Manual testing did not reveal it because manual testing is not as extensive as automated testing. I was able to discover the error in a test that screened my TicTacToe for all clickable combinations - something I would never do by hand.

All clickable combinations in Tic Tac Toe are 255168. This number can vary, depending on your definition of how unique a combination has to be. I pulled my test numbers from [an article on the web](www.google.de/#q=255168+Tic+Tac+Toe). If you generate all clickable combinations and use them to automatically play the game you should get:

- 0 games ending in zero, one, two, three and four moves.
- 1440 games ending with a winner in 5 moves.
- 6 moves -&gt; 5328 games
- 7 moves -&gt; 47952 games
- 8 moves -&gt; 72576 games
- 9 moves -&gt; 81792 games and
- a draw result for 4868 games.

Using this data you can create a test that tests all combinations and reveals all coding errors in the win condition code.

Before you can actually use *NUnit* as a test framework to implement the tests, there are a couple of things you have to do first.

1. Get [NUnit](www.nunit.org)
2. Create a new project that will contain your tests
3. Link to
    - the project you want to test
    - the NUnit framework (*NUnit-???\bin\nunit.framework.dll*)
    - the NUnit GUI (*NUnit-???\bin\lib\nunit-gui-runner.dll*)
    - the Rhino framework (*NUnit-???\bin\lib\Rhino.Mocks.dll*)
4. Switch your project target framework to "Framework 4" - required for Rhino Mocks
5. Edit the assembly of the project under test and tell it to allow your test project to view [internals](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.internalsvisibletoattribute.aspx)
6. Create your main method to launch NUnit using the debugger.

I guess that a lot of the above steps are unclear to most, so I will elaborate a bit.

*Getting NUnit* should not be a problem - you basically download it and unzip it. You will now face the choice of putting it into your program directory making developement easier and more portable, or putting it outside of your project making it easier to reuse for other projects. The choice is yours.

*Create a new project:* See to it, that it is in the same folder as the production code and keep it in the same solution folder so you can switch between them easily. The NUnit GUI is a windows forms application as far as I can tell. To ensure that all the required assemblies are already linked, you want to start this project as a forms application. You will want to delete the initial empty form since there is no point in keeping it.

*Link all the dependencies:* If you started the project not as a forms application you have to link those too. Otherwise you just need to link the things listed above. Initially you will not need the *Rhino framework*. It is a mocking framework that is quite easy to use and it comes with NUnit. I've never written meaningful code without wanting to mock something during testing, so you might as well link to it now.

*Switch your profile:* The default profile is a client profile - this may have advantages that I am unaware of but in combination with Rhino and other assemblies you may experience errors, if you do not change the target to "*.Net framework X*".

*Editing the assembly* is something I usually do, but did actually skip for TicTacToe. Without this your test framework can only see the public portions of the assembly. If you add *[assembly: InternalsVisibleTo("")]* to it this changes and the test assembly can also see the internals ot the assembly under test. That in turn allows you to control what portions are visible in the production code, since you don't have to make everything public if you want to run a test on it.

Create your main method. The windows forms project comes with an application start. You wnat to edit that so it looks like this:
<pre>namespace TicTacToeTest
{
    static class Program
    {
        [System.STAThread]
        static void Main()
        {
            string fullPath = System.Reflection.Assembly.GetAssembly(typeof(Program)).Location;
            NUnit.Gui.AppEntry.Main(new string[] { fullPath });
        }
    }
}</pre>
This calls the NUnit GUI runner with the appropriate location of your test assembly. This is written universal so you can actually use it for all your test projects. The reason why you want to do this is because you use the *Express* version of *Visual Studio*. This version does not allow you to attach the debugger to a running assembly. So what you have to do is to start the assembly using the debugger - that way you can actually debug your test should anything go wrong. The downside is that you will get notified if an exception is thrown - even if that was intentional.

I do not include a playable link to the executeable. I think if you read it this far you may want to look at the sourcefiles instead. So here is the project instead. [Human vs. Human version 001](http://blog.aypahyo.net/tictactoe/TicTacToeV001.zip)
