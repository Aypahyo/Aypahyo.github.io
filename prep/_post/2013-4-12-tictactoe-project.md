---
layout: post
title: tic tac toe project
---

The weekend is near and I have to decide how I will go about that TicTacToe game.

1. The first question I need to answer is what programming language to use.
## Ansi C89/90
I am forced to use this during my week in my daily work. So doing TicTacToe in it would be a nice additional training. I may upload some thoughts about clean programming in C90 in the near future. My private time is very limited so I'd like to spend it efficiently. For that reason alone C90 is not an option.
## C++ 11
I used to like C++ and I am understanding it even better now that I am forced to live without its comfort. C++ is important for modding FPS-Shooter-Games so that puts it on the top of my list for "need to train" and I may add a project in the future.
## Java
Java is a very easy language. Always state of the art. Everything is free including refactoring tools. Using Eclipse as an IDE is cool because of the huge amount of automated upgrades. Considering Android and the future of the mobile plattform, it seems to be a good idea to use Java for a Tic Tac Toe app project in the future.
## C#
Last but not least - I personally develope fastest in C# and there is a tool I never used and that I would like to learn. So C# will be my choice for this project.

2. The second question is about tools.

I plan on programming TicTacToe and deleting it afterwards from my harddrive, there will be a copy on this server - so I wont use a repository. For testing I will create a seperate project that connects to NUnit and gets access to internals of TicTacToe. I also  want to see *FXCop* from Microsoft in action. *FXCop* is a styleguard and will help me to conform to the C# default coding style. On the framework side of things I can currently only think about *WPF* for the GUI, the alternative would be XNA and i think that may be quite some overkill for this project.

1. The third question is how to develop the game.

So this is the last question before I start tomorrow. I am thinking what features to include and what I actually want to do. I just opened MS Paint and started sketching my program. As with any first draft this will change. I can already see that my integrationtests can fully run through the viewmodel. I also like the idea of having my map contain a representaion of the winning lines, this will make the rulepackage for the ruledriven AI easier. The map will also contain the same playertoken over and over again. Since the map already contains winning lines that will contain tokens I am unsure if the field concept will benefit me in the end. I think the fields are an implementation detail that will be superfluis.

I have to think a little bit more about how I want the players to make their move. The idea is that the gamecontroller triggers the move but that may not be possible. It could be that the gamecontroller should actually be a gameservice that accepts or denies moves and sends tokens in events to inform everybody that a new player earned the right to move - if I can get it done with a controller I will go for it, otherwise the players will be observers and the gamecontroller will be an observeable.

Here is my fingerpainted picture for your delight. Feel free to comment, especially if you have designimprovements for me.

<img alt="" src="http://blog.aypahyo.net/pictures/TicTacToe_FirstDraft.png" />
