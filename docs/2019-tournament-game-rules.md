# Tournament Game Rules
This document sets out the game rules for the tournament battles.

Please note, that we reserve the right to change game rules up until the day of the tournament.

## Board Rules
- Boards will always be square.
- Boards will be either small (7x7), medium (11x11) or large (19x19)
- A minimum of 2 snakes can be put on a board.
- A maximum of 8 snakes can be put on a board.
- Starting positions are predetermined to put snakes as far away from each other and 1 square away from walls.
- Starting positions are randomly assigned to a snake from the available numbers as shown below.  The numbers 
illustrate the way in which the board will be filled with snakes.  For example, if there are only two snakes in the battle, 
only positions 1 and 2 will be filled.

### Illustrative Starting Positions (7x7 board)
``` 
-----------------------------
|   |   |   |   |   |   |   | 
-----------------------------
|   | 1 |   | 5 |   | 4 |   | 
-----------------------------
|   |   |   |   |   |   |   | 
-----------------------------
|   | 8 |   |   |   | 6 |   | 
-----------------------------
|   |   |   |   |   |   |   | 
-----------------------------
|   | 3 |   | 7 |   | 2 |   | 
-----------------------------
|   |   |   |   |   |   |   | 
-----------------------------
```

## Food Spawn Rules

- The amount of food on a board will be set at the beginning of a game.  
- You will not know how much food will be on the board before the game, however it is guaranteed that the amount of food will be at least 1.
- The same amount of food will remain on the board at all times.  That is, if a piece of food is eaten by a snake, a 
new piece of food will be available on the board in the next turn.
- Food will spawn at random coordinates*

\* There may be, or may not be, additional logic added before the tournament, to prevent food from apearing directly in front of a snake.

## Turn Mechanics
1. The engine randomly appoints a snake to a starting position from the eight predetermined starting points.  (See Starting Positions) 
1. Each snake is sent a /start request with the board, food, and snake information. (SnakeRequest).
   - Each snake is required to respond with a StartResponse.  
   - Snakes that do not respond within 1 second to the start request are considered dead.
3. Each snake, in parallel, is sent a /move request with the board, food and snake information. (SnakeRequest).
   - Each snake is required to respond with a MoveResponse.  
   - Snakes that do not respond within 500ms to the move request will continue to move in the same direction as their last move.  
   - If this is the first move and there is no response from the snake, the direction will default to up.
1. After all the snakes have returned their move decision the engine will, for each snake,
   - Move head by adding a new body part at the start of the body array in the move direction
   - Reduce health
   - Check if the snake ate and adjust health
   - For any snake that did not eat, remove the final body segment
   - Check for snake death (see Snake Deaths)
   - Add new food for any food that was eaten
1. Dead snakes are sent the /end request.
1. If there are two or more snakes alive, go to step 3.
1. If there is a single snake alive send the /end request to it.

## Snake Deaths
Snakes can die in a variety of ways. 

- Wall collision. You hit a wall.  Try turning.
- Snake self-collision. You hit yourself. Try turning less.
- Snake collision. You hit another snake.  Try to avoid snakes.
- Starvation.  You ran out of health.  Try to find food.
- Head on collision:
  - Head on collision of unequal length snakes - shorter snake dies.
  - Head on collision of equal length snakes - both snakes die.

## General Tournament Rules
- Each team may only enter 1 snake into the tournament.
- Snakes should be healthy.  A healthy snake is one that responds to `/ping` requests.

### Divisions
- There are three divisions in the battlesnake tournament.  Beginner, Intermediate, Advanced.
- The beginner division: For those new to programming or who want to improve their skills. You’ll be provided with some sample code to get you started.
- The intermediate division: You know how to slither and are ready to compete.
- The advanced division: For professionals and experienced developers. You’ll also be competing against the best of the best, so come prepared for fierce competition.
- A separate tournament will be run for each division.

### General Tournament Rules
- All of the snakes in a division will be grouped into the largest possible groups of snakes that is less than or equal to 8.
For example, if there are 21 snakes in a division, there will be 3 groups of 7, not two groups of 8 and one group of 5.

### Beginner/Intermediate Division Tournament Rules
- 2 games will be run for each group.
- The winner of the first game will go through to the next round.
- The winner of the second game will go through to the next round.
- If there is a tie, that is, the last two snakes are of equal length die on the same turn, the game will be re-run.

### Advanced Division Tournament Rules
TBD

### Beginner/Intermediate/Advanced Tournament Finals
Once there is a game with only 1 group the last round has been reached and this is the final.
- The final is four games.  
- The winners of games 1, 2 and 3 go through to the fourth game.
- The three snakes that made it through to game four compete for 1st, 2nd and 3rd place.
- If any two snakes die on the same turn, the winner is the longer snake.
- If there is a tie, that is, two snakes are of equal length die on the same turn, the game will be re-run.

## Technical Rules and Considerations
The following section details some special cases that you should be aware of for the tournament.
- Your snake may have simultaneous games running against it. 
- Your snake must implement the `/ping` endpoint to enter into the competition.  Snakes that are not responding to `/ping` will be removed from the competition.
- It is the snake authors responsibility to ensure that your snakes can handle long pauses, maybe up to minutes at a time.
