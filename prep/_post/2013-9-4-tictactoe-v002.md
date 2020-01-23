---
layout: post
title: tic tac toe v002
---

## Where do we want to be?
The featurelist of the game we want to have contains several AIs, selectable tokens and four gamemodes. The idea is to see what impact those requirements have on our architecture.
## Where did we start?
We started with a single God Object, no testing and no style control. Adding those cleaned up the god object, revealed some bugs and enabled easy refactorings. We are now in a state where the architecture is quite ugly but we can safely change the code in a reasonable time.
## Where are we now?
<img alt="model of the tictactoe programming project" src="http://blog.aypahyo.net/tictactoe/TicTacToeV002.png" />

The current state is V002. It features some refactorings towards our next feature but nothing major. It consists of three Projects, each serving a specific role. The GUI project contains all GUI relevant coding including the adaption to the game. The game project contains all game logic that can be seperated from the GUI. Last but most improtant is the testing project, featuring all tests that verify the game is working as intended. In its current version it acts on the gui just like the user would and controls the result.
## What will we do next?
We will implement an easy AI so we can have fun playing against it and also because it is a step towards our final goal. In an easy game like this there is basically no need for the AI to cheat (I personally like games where the AI does not cheat ). That means that we can create an AI that uses the game just like a human would use the game. To make it easier we will route all userinteraction throug an interface ( IPlayer ) and make the human a concrete implementation of that interface. Once that is complete it will be quite easy to create an AI that randomly clicks buttins until the game is over.
