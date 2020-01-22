I recently got married and had a lot of other stuff do do as well. My apologies for not releasing a new post in a while.

Now I did not get around to try out <em>FX Cop</em> so that remains on my todo list. But I added some unit tests to my project.

I coded the TicTacToe the dirty way without test in my first iteration. There are a couple of downsides to it - the worst being that you release untested code to your customer - basically telling your customer that you did not ensure that you actually did the job he asked you to do.

I write this because during introducing tests I noticed a quite sever programming bug. The rules that check if a win condition was triggered had an error. Manual testing did not reveal it because manual testing is not as extensive as automated testing. I was able to discover the error in a test that screened my TicTacToe for all clickable combinations - something I would never do by hand.

All clickable combinations in Tic Tac Toe are 255168. This number can vary, depending on your definition of how unique a combination has to be. I pulled my test numbers from <a title="link to article" href="www.google.de/#q=255168+Tic+Tac+Toe" target="_blank">an article on the web</a>. If you generate all clickable combinations and use them to automatically play the game you should get:
<ul>
	<li>0 games ending in zero, one, two, three and four moves.</li>
	<li>1440 games ending with a winner in 5 moves.</li>
	<li>6 moves -&gt; 5328 games</li>
	<li>7 moves -&gt; 47952 games</li>
	<li>8 moves -&gt; 72576 games</li>
	<li>9 moves -&gt; 81792 games and</li>
	<li>a draw result for 4868 games.</li>
</ul>
Using this data you can create a test that tests all combinations and reveals all coding errors in the win condition code.

Before you can actually use <em>NUnit</em> as a test framework to implement the tests, there are a couple of things you have to do first.
<ol>
	<li>Get <a title="nunit mainpage" href="www.nunit.org" target="_blank">NUnit</a></li>
	<li>Create a new project that will contain your tests</li>
	<li>Link to
<ul>
	<li>the project you want to test</li>
	<li>the NUnit framework (<em>NUnit-???\bin\nunit.framework.dll</em>)</li>
	<li>the NUnit GUI (<em>NUnit-???\bin\lib\nunit-gui-runner.dll</em>)</li>
	<li>the Rhino framework (<em>NUnit-???\bin\lib\Rhino.Mocks.dll</em>)</li>
</ul>
</li>
	<li>Switch your project target framework to "Framework 4" - required for Rhino Mocks</li>
	<li>Edit the assembly of the project under test and tell it to allow your test project to view <a title="InternalsVisibleTo" href="http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.internalsvisibletoattribute.aspx" target="_blank">internals</a></li>
	<li>Create your main method to launch NUnit using the debugger.</li>
</ol>
I guess that a lot of the above steps are unclear to most, so I will elaborate a bit.

<strong>Getting NUnit</strong> should not be a problem - you basically download it and unzip it. You will now face the choice of putting it into your program directory making developement easier and more portable, or putting it outside of your project making it easier to reuse for other projects. The choice is yours.

<strong>Create a new project:</strong> See to it, that it is in the same folder as the production code and keep it in the same solution folder so you can switch between them easily. The NUnit GUI is a windows forms application as far as I can tell. To ensure that all the required assemblies are already linked, you want to start this project as a forms application. You will want to delete the initial empty form since there is no point in keeping it.

<strong>Link all the dependencies:</strong> If you started the project not as a forms application you have to link those too. Otherwise you just need to link the things listed above. Initially you will not need the <em>Rhino framework</em>. It is a mocking framework that is quite easy to use and it comes with NUnit. I've never written meaningful code without wanting to mock something during testing, so you might as well link to it now.

<strong>Switch your profile:</strong> The default profile is a client profile - this may have advantages that I am unaware of but in combination with Rhino and other assemblies you may experience errors, if you do not change the target to "<em>.Net framework X</em>".

<strong>Editing the assembly</strong> is something I usually do, but did actually skip for TicTacToe. Without this your test framework can only see the public portions of the assembly. If you add <em>[assembly: InternalsVisibleTo("")]</em> to it this changes and the test assembly can also see the internals ot the assembly under test. That in turn allows you to control what portions are visible in the production code, since you don't have to make everything public if you want to run a test on it.

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
This calls the NUnit GUI runner with the appropriate location of your test assembly. This is written universal so you can actually use it for all your test projects. The reason why you want to do this is because you use the <em>Express</em> version of <em>Visual Studio</em>. This version does not allow you to attach the debugger to a running assembly. So what you have to do is to start the assembly using the debugger - that way you can actually debug your test should anything go wrong. The downside is that you will get notified if an exception is thrown - even if that was intentional.

I do not include a playable link to the executeable. I think if you read it this far you may want to look at the sourcefiles instead. So here is the project instead. <a href="http://blog.aypahyo.net/tictactoe/TicTacToeV001.zip" target="_blank">Human vs. Human version 001</a>