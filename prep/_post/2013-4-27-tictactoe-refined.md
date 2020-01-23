I talked with a friend about the design and he pointed out that it seems too big for such a small game.

I have to admit that it is not obvious why I would go for that design. 
To illustrate why I want to do certain things he suggested to start with one godlike class. 
In successive expansions I will elaborate the design and install all those small objects. 
That way it will be easy to explain in small steps why I want them to be there.

The godobject was done so fast I did not even set up the whole project. 
I started with the view and did miss the point where I should have started to switch to [TDD](https://en.wikipedia.org/wiki/Test-driven_development). 
For the same reason I missed the point where I should have tried out [FXCop](http://en.wikipedia.org/wiki/FxCop).

The godobject is now coded into the [MainWindow-ViewModel](http://blogs.msdn.com/b/dphill/archive/2009/01/31/the-viewmodel-pattern.aspx). 
From a design point of view I coded the business logic into the layer that is meant to help isolate the logic. 
It shows how easy it is to get sucked into the [WPF](http://en.wikipedia.org/wiki/Windows_Presentation_Foundation) framework since it is so powerful.

Everything was very straigth forward except for one problem I discovered. 
During the project I recognized that I am unable to use data binding on a multidimentional array. 
This actually created an ArgumentException. 
I posted [the problem on stackoverflow](http://stackoverflow.com/questions/16119200/wpf-binding-to-multidimensional-array-in-the-xaml) 
I may come back to this problem after I finished TicTacToe. 
Jagged arrays work just as fine, so I don't want to divert from my goal too much. 
Post your suggestions in the comments or answer on stackoverflow (easy points for the taking).

You can play the 
[Human vs. Human version 000](http://blog.aypahyo.net/tictactoe/TicTacToeGUIv000.exe)
, it looks like this:

<img alt="" src="http://blog.aypahyo.net/tictactoe/TicTacToe_GUI_V000.png" />

The view itself consists of 9 buttons and a status text. 
Each button has a viewbox with a TextBlock inside instead of its regular content. 
This allows the X and O characters to scale up to whatever size the button is. 
In addition I use the buttons name to identity it after the click. 
All buttons call the mapClick on click.

I dont show the [XAML](http://en.wikipedia.org/wiki/Extensible_Application_Markup_Language) here because I have bad experience with XML like content on this page. 
Post in the comments if you have a good solution for posting XML snipptes on wordpress.

The [codebehind file](http://msdn.microsoft.com/library/vstudio/aa970568#codebehind_and_the_xaml_language) of the main window is very slim. 
This is intentional to get to out of that file as fast as possible. 
For testing purposes you want this file small because you can not reasonably instantiate it. 
In this case I chose to create a Viewmodel inside the constructor. 
The alternative is to edit the APP and change the way the Mainwindow is created. 
There is usually some temporal coupling to [InitializeComponent()](http://stackoverflow.com/questions/245825/what-does-initializecomponent-do-and-how-does-it-work-wpf). 
Here for instance you have to set the mapClick action before the call or a NullPointer exception occurs.
<pre>    public partial class MainWindow : Window
    {
        MainWindowViewModel vm;
        private Action&lt;object, RoutedEventArgs&gt; mapClick;
        public MainWindow()
        {
            this.DataContext = vm = new MainWindowViewModel();
            mapClick = vm.mapClick;
            InitializeComponent();
        }
    }</pre>
The [MainWindow-ViewModel](http://blogs.msdn.com/b/dphill/archive/2009/01/31/the-viewmodel-pattern.aspx) contains all the rest of the game. 
 First I show it here and afterwards I talk about the things that are wrong with it.
<pre>    class MainWindowViewModel : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;
        public Char[][] MAP { get { return _map_jagged; } }
        public String Systemstate { get {
                if (' ' != _winner) return "Player " + _winner + " has won the game.";
                if (turn &gt; 9 ) return "The game ended in a tie.";
                return "Player " + _icons[(turn)% 2] + " has to move.";
            } }

        private void notify(String property) 
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this, new PropertyChangedEventArgs(property));
                PropertyChanged(this, new PropertyChangedEventArgs("Systemstate"));
            }
        }

        private Char[][] _map_jagged = {
                                       new Char[]{ ' ', ' ', ' ' }, 
                                       new Char[]{ ' ', ' ', ' ' }, 
                                       new Char[]{ ' ', ' ', ' ' }
                                       };

        private uint turn = 1;
        private Char[] _icons = { 'O', 'X' };
        private Char _winner = ' ';

        public void mapClick(object sender, RoutedEventArgs e)
        {
            if (sender is Button)
            {
                String name = (sender as Button).Name;
                int col = name[3] - '0';
                int row = name[4] - '0';
                mapProcess(col, row);
            }
        }

        public void mapProcess(int col, int row){
            if (GAMETryMarkMap(col, row, _icons[turn % 2]))
            {
                notify("MAP");                
            }
        }

        private bool GAMETryMarkMap(int col, int row, Char mark)
        {
            bool changed;
            if (' ' == _map_jagged[col][row] &amp;&amp; ' ' == _winner)
            {
                GAMEMark(col, row, mark);
                changed = true;
            }
            else
            {
                changed = false;
            }
            return changed;
        }

        private void GAMEMark(int col, int row, Char mark)
        {
            uint n;
            _map_jagged[col][row] = mark;
            ++turn;
            for (n = 0; n &lt; 3; ++n)
            {
                if (_map_jagged[n][0] != ' ' &amp;&amp; _map_jagged[n][0] == _map_jagged[n][1] &amp;&amp; _map_jagged[n][0] == _map_jagged[n][2]) _winner = _map_jagged[n][0];
                if (_map_jagged[0][n] != ' ' &amp;&amp; _map_jagged[0][n] == _map_jagged[1][n] &amp;&amp; _map_jagged[0][n] == _map_jagged[2][n]) _winner = _map_jagged[n][0];
            }
            if (_map_jagged[0][0] != ' ' &amp;&amp; _map_jagged[0][0] == _map_jagged[1][1] &amp;&amp; _map_jagged[0][0] == _map_jagged[2][2]) _winner = _map_jagged[0][0];
            if (_map_jagged[0][2] != ' ' &amp;&amp; _map_jagged[0][2] == _map_jagged[1][1] &amp;&amp; _map_jagged[0][2] == _map_jagged[2][0]) _winner = _map_jagged[0][2];
        }
    }</pre>
The easy things to say about it is that this object has a multitude of responsibilities. You can even see that some methods start with the name of an object they want to be connected with.

Systemstate is a generated string for the UI. 
It looks like a seperate object or concept that actually wants to exist. As a matter of fact t
he game can be represented by using a [finite-state machine](https://en.wikipedia.org/wiki/Finite-state_machine). 
At the point where the viewModel wants to get the state the call should look more like one of the following options:
<pre>public String Systemstate { get { return game.getState.ToString(); } }
public String Systemstate { get { return game.state.ToString(); } }
public String Systemstate { get { return game.stateString; } }</pre>
The map is an implementation detail that should be burried somewhere in the game object. 
It looks like the MainWindow-View wants a jagged array, but the adaption is what the viewModel is for - not the game itself. 
The game should not concern itself with how to implement the map. 
When we are done with the Map it will likely not be a jagged array but something else.
It is likely that using a [LinQ](http://en.wikipedia.org/wiki/Language_Integrated_Query) query will be easiest to implement the adaption.

All the small things like turn or playertoken should not be in the MainWindow-ViewModel. 
Maybe some of them should be part of a state, player, map or game.

The playertokens do bother me. 
There are three tokens in play at the moment: '<strong>X</strong>', '<strong>O</strong>' and <strong>' '</strong>. 
The blank token is used to identify that no token is on the field. 
In the current design I have to check so that blank token does not win the game. ( _map_jagged[.][.] != ' ' ). 
This can actually be made to disappear with better design.

The players themselves are hidden in the tokens and in the turn variable. 
This is a problem because I can not add new versions of players. 
So in order to get the AI players in the game I have to extract the players.

Exploring what's wrong is fun but right now one has to look at the backlog and ask what to do next. 
I will start with getting everything under test, since this is extremely important for refactoring. 
After tests I want to have that FXCop watching over me, so I will get that installed. 
The third step is to move everything out of the viewmodel into a game object. 
And at that point I will take another look at the design.