I was just looking at some old projects and found a TicTacToe game i made at some point in the past. The code style is pretty bad and i am thinking about remaking it. I think it will be a nice little project that lends itself to my occasional spare time programming.

The thing about my old implementation that I wanted to share is the AI. It is a very simple script that was fun to play against as I remember. Four rules are sequentially checked, whatever rule applies first determines the selected field, and the AI makes its move accordingly.

The rules are:
<ol>
	<li>Win in one move possible? → win</li>
	<li>Enemy win possible? → block</li>
	<li>Center still available? → take center</li>
	<li>Any free fields? → randomly pick a free field</li>
</ol>
As I remember it the AI was quite pleasant. Good enough to not be boring. Bad enough to be beatable.

The AI will not hesitate to win (1). The player has to lay out a trap that consists of more than one win opportunity (2). The AI will try to get the strongest map position (3). No two Games are the same (4).

My next implementation will have a couple of different options.
<ul>
	<li>Easy: A perfect AI trying to loose</li>
	<li>Normal: A random selecting AI</li>
	<li>Hard: The above AI</li>
	<li>Impossible: A perfect AI trying to win</li>
</ul>
I will start a mini series for TicTacToe on this blog, if you have suggestions for the implementation feel free to comment.