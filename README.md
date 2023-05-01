# Adverserial_AI
## How to run

In you terminal type: python -m referee 10 team_name team_name2

Usage: referee [-h] [-V] [-d [delay]] [-s [space_limit]] [-t [time_limit]] [-D | -v [{0,1,2,3}]] [-l [LOGFILE]] [-c | -C] [-u | -a] red blue n
 
### Mandatory:
 
  n&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;size of the game board  
  red&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;location of Red's Player class (e.g. package name)  
  blue&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;location of Blue's Player class (e.g. package name)  
  
### optional arguments:
  -h, --help&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;show this message.

  -V, --version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;show program's version number and exit
  
  -d [delay], --delay [delay]
                        how long (float, seconds) to wait between game                    
                        turns. 0: no delay; negative: wait for user input.
                        
  -s [space_limit], --space [space_limit]
                        limit on memory space (float, MB) for each player.
                       
  -t [time_limit], --time [time_limit]
                        limit on CPU time (float, seconds) for each player.
                        
  -D, --debug           switch to printing the debug board (with
                          more information) (equivalent to -v or -v3).
                          
  -v [{0,1,2,3}], --verbosity [{0,1,2,3}]  
                        control the level of output (not including output                        
                        from players). 0: no output except result; 1:
                        commentary, but no board display; 2: (default)                        
                        commentary and board display; 3: (equivalent to -D)                        
                        board display with extra information.
                        
  -l [LOGFILE], --logfile [LOGFILE]  
                        if you supply this flag the referee will create a                        
                        log of all game actions in a text file named LOGFILE                        
                        (default: game.log).
                        
  -c, --colour          force colour display using ANSI control sequences  
                        (default behaviour is automatic based on system).
                        
  -C, --colourless      force NO colour display (see -c).
  
  -u, --unicode         force pretty display using unicode characters  
                        (default behaviour is automatic based on system).
                        
  -a, --ascii           force basic display using only ASCII characters (see
                        -u).
  
  ## Rules of the Game
  
Cachex is a perfect-information two-player game played on an n × n rhombic, hexagonally tiled
board, based on the strategy game Hex. Two players (named Red and Blue) compete, with the
goal to form a connection between the opposing sides of the board corresponding to their respective
color.

![image](https://user-images.githubusercontent.com/124246311/235448918-efeebf5c-5a44-4885-bf5b-e4b4c27fa433.png)

• The game begins with an empty board and proceeds sequentially. 

• By convention, Red starts. Throughout the game Red and Blue take turns placing stones on
empty hexagonal cells (hexes).

• The game ends when one player forms an unbroken chain of stones on adjacent hexes between
their respective sides; this player wins the game. The hexes at each of the four corners belong
to both players.

• Pairs of tokens may be removed from the game through a capture mechanism (Figures 3 and
4). If a 2 × 2 symmetric1 diamond of cells is formed consisting of two stones from Red and
Blue each, the player who completed the diamond removes their opponent’s stones from the
game. Note that:

&nbsp;&nbsp;&nbsp; – Either player may exploit the capture rule, and the capture rule applies for all possible
orientations of the diamond found on the gameboard.

&nbsp;&nbsp;&nbsp;– The capture mechanism only applies to a diamond formed by 2 Red and 2 Blue stones -
it does not apply if there are three of one color and one of the other.

&nbsp;&nbsp;&nbsp;– If multiple diamonds of valid type are formed by placement of a single stone on the
board, all of the opponent’s stones in the just-formed diamonds are removed from the
board.

&nbsp;&nbsp;&nbsp;– After a capture, the opposing party can immediately threaten a re-capture by placing a
piece on one of the recently-captured positions.

• To mitigate first-mover advantage, the swap rule applies (Figure 2). Once Red completes their
first move, Blue may choose to proceed as normal and lay down a blue stone, or steal Red’s
move for their own, reflecting the position of Red’s stone along the major axis of symmetry
(i.e. interchanging the row and column index) and changing the stone from red to blue. The
game proceeds as normal, with Red playing next. The swap rule incentivizes the first player
to play as fair a move as possible

&nbsp;&nbsp;&nbsp;- if the first move is too strong, the second player is able to
steal the advantage2. For fairness, starting with a hex in the center of the board is illegal.



![image](https://user-images.githubusercontent.com/124246311/235448993-d2444333-68e9-452f-a78a-896ab3b8a429.png)


Figure 2



![image](https://user-images.githubusercontent.com/124246311/235449006-0005d938-0632-489c-acdb-858e760e8133.png)

Figure 3


## Approach

I utilised A star algorithm to find the best linear path to the end of the board to start off. As through research I did not find a more optimal algorithm that expands fewer nodes than A* search.

This worked by creating priority queues of nodes to be expanded every turn, and every time I called the search function, I loop through these nodes to function. This heuristic function would then get subtracted by one every time the search would traverse over a hexagon in which there was already a token placed by the player. I decided to keep all the weights of the nodes for the heuristic function equal, where the heuristic was the Manhattan space between the current state and the goal state. As I were required to create graphs for each search path, I cleared each graph, but kept it stored in memory after each turn to minimize the space required.

It is worth noting that when I implemented <bold>temporal difference learning</bold> and <bold>gradient descent learning</bold> to find the most optimal weights of the heuristic, I found that the agent performed better without the use of these machine learning algorithms. This can be accounted for by the fact that training each path set proved inefficient and time consuming. I managed to only produce win-rates of ~40% against adversarial opponents, which proved a decrease in overall agent effectiveness.

I decided to place imaginary goal and start nodes at each end of the board, where each node from (0,0) → (0,n), (0,n) → (n,n), (n,n) → (0,n), (0,n) → (0,0) would be connected to imaginary nodes at ![image](https://user-images.githubusercontent.com/124246311/235451233-40ab6ea7-6c13-4424-9b18-6c81d3d50c13.png) respectively, where n is the board size. This managed to minimize the computation time exponentially, as the search heuristic would not need to do computations for all possible paths, but only all paths that are converging to a singular state and hence, the time constraint was minimized.

I also implemented a MiniMax algorithm, where the AI attempts to look at future moves both it and the opponent can take, while also removing and filtering poor moves the agent would sometimes decide. From this, I also allowed the agent to simulate moves before the opponents moved which would lead to a defensive strategy at times. In the case that the moves were too defensive, the depth limit and cut-off test of the minimax algorithm seemed to mitigate this effect.

the MiniMax algorithm with a depth-limit of 3., as unfortunately, a depth limit of larger than 3 led to a divergence in the branching factor. When running MiniMax over the entirety of the game, I found that the run time was too inefficient for the constraints of the agent. Hence, we decided I would run a linear A* search for the first ⌊ ⌋ turns, and move to MiniMax the remaining 𝑛 2 turns. This seemed to improve the AI’s efficiency, and provided a good balance between resource expensive moves versus greedy moves. This can be thought of as a Start - Middle - End approach to the agent. It is obvious that it is more beneficial to place tokens away from the edges at the start of the game, as there is more flexibility in path choice and since there are many more moves possible at the beginning of the game, I require a smaller depth - limit. I gradually increase the depth limit based on the state of the game.

the MiniMax traverses over all hexagons that intersect with the opponents best path within A* search, and if it goes over tiles that may already be present, I subtract 1 from the heuristic. I did this to provide a better estimate of how far the search is from the goal state. As well as this, the algorithm will attempt to block the enemy’s moves while continually attempting to follow the player's most optimal path, leading to the best outcome for the player.

The evaluation function helped determine what path and subsequent choices within the minimax function were to be taken. With this in mind, to help quantitatively decide whether a board state was desirable or not. The decision was made that the evaluation function would calculate the least amount of hexagons required to make a connecting path, and the difference between the amount of tokens in total on the board. The reasoning behind this was to give an incentive to the minimax to choose options that either progress the player forward to a win through using the distance comparison and to block the opponent. The amount of token in the evaluation would also help in trying to create diamonds, however this would not mean the AI will go out of its way to achieve this as the heuristic further supports the action to create a connecting path. If blocking the opponent is on its way to the end goal however it will choose that as its best option.



