### Use Cases
*Choose Configuration*
Participating actor: initiated by user

Entry condition
- A game is not in progress

Exit Criteria
- The board has been updated to the selected configuration

Flow of Events
1. User selects one of the three configurations
2. SquarePush begins a game with the matching configuration and refreshes the display

*Move Ninja-se* 
Participating actor: initiated by user

Entry condition
- A game is in progress

Exit Criteria
- ninja-se has been moved one space in the direction requested by the user
	- when ninja-se moves, any blocks in his way are pushed in that direction too
	- any blocks that are pushed against an edge of the board wrap around to the other side of the board


Flow of Events
1. user selects a valid intended direction for Ninja-se to move
2. SquarePush attempts to move ninja-se and the blocks they push

*Remove Group*  
Participating actor: initiated by user

Entry Condition
- 2x2 square of same color is present on the display
- A game is in progress

Exit Condition
- the 2x2 square of same color is removed from the display

Flow of Events
1. user initiates the removal of all 2x2 same color blocks
2. SquarePush replaces all 2x2 same color blocks with 

*Reset Game*  
Participating Actor: initiated by user

Entry Condition
- A game is in progress

Exit Condition
- board has been reset to its original configuration 

Flow of Events
1. User initiates board reset
2. SquarePush resets the state of the board to the initial configuration. 
*Complete Game*
Participating Actor: initiated by user

Entry Condition
- A game is in progress
- all colored blocks have been removed from the board

Exit Condition
- The game is completed 

Flow of Events
1. user initiates completion of the game after all of the colored blocks have been eliminated
2. SquarePush displays some indication of completion and refrshes the display.
