---
layout: post
title: tic tac toe ai
---

I was just looking at some old projects and found a TicTacToe game i made at some point in the past. The code style is pretty bad and i am thinking about remaking it. I think it will be a nice little project that lends itself to my occasional spare time programming.

The thing about my old implementation that I wanted to share is the AI. It is a very simple script that was fun to play against as I remember. Four rules are sequentially checked, whatever rule applies first determines the selected field, and the AI makes its move accordingly.

The rules are:

1. Win in one move possible? → win
2. Enemy win possible? → block
3. Center still available? → take center
4. Any free fields? → randomly pick a free field

As I remember it the AI was quite pleasant. Good enough to not be boring. Bad enough to be beatable.

The AI will not hesitate to win (1). The player has to lay out a trap that consists of more than one win opportunity (2). The AI will try to get the strongest map position (3). No two Games are the same (4).

My next implementation will have a couple of different options.

- Easy: A perfect AI trying to loose
- Normal: A random selecting AI
- Hard: The above AI
- Impossible: A perfect AI trying to win

I will start a mini series for TicTacToe on this blog, if you have suggestions for the implementation feel free to comment.
