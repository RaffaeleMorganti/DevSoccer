
# DevSoccer
A Javascript game about programming a soccer team
# Overview
DevSoccer is a javascript game in which the user can submit a javascript file, containing the istructions which will serve as the players AI of one of the teams, in a soccer match played in an HTML 5 canvas. Your AI can compete with any other AI written by other developers.
The game will create 11 players for each of the two given AIs, it will give them the needed info about the match and it will ask each "player" what to do for each turn (or frame) of the match
# Instructions
### basics
The submitted file should contain a javascript prototype called CustomPlayer, this prototype should have two functions: `init(team, number, matchinfo)` and `turn(status)`.

The `init` function is called once per game, at the beginning and should return an array of two integers which will be the starting coordinates of that player (see Rules paragraph to know what these should be exactly).  
The three arguments give you basic information about the match and the player:  
team (`0` or `1`) represents the team you are on.  
number (integer from `0` to `10`) represent the index of the player in the team. Note that player 0 will be you goalkeeper, more on that later.  
matchinfo is an object which will contain useful information about the setup of the match, like the size of the playing field and more (check the matchinfo paragraph to know exactly what do they mean)

The `turn` function is called once per frame and should return an object with info about what the player will do for that frame, what a player can do will be explained in the next section.  
The argument is an object which contains all information about the current state of the game, that is position and movement of each player and the ball.

For reference about the arguments passed to these two function refer to [./AI/README.md](./AI/README.md)  
A [template](./AI/template.js) for the file you should submit is included in the [AIs Section](./AI).    
once you are done writing you AI (or you want to test it), open index.html, select the two AI files and click "Start Game".

the players and the ball always move on the field with a given speed (the number of units that entity moves per frame) and the coordinates start at (0,0) in the top left corner, this means that moving with positive x speed means moving to the right, negative x speed means left, positive y means down and negative y means up.

The objective of each team is (obviously i would say) to put the ball in their opponents goal, and defend their one.  
The goals are placed at `x = 0` and `x = MAXX` (the left and right edges of the field), and their width and Y coordinates are supplied in the variables `GOALSIZE` and `GOALCOORDINATES`.  
Team0 starts in the left side of the field must defend the goal at `x = 0` and score in the goal at `x = MAXX`, team1 starts on the right side of the field and... you get it.

### turns and what you can do
in DevSoccer each frame every player decides what he should do next. A player can increase/decrease his speed  in a given direction and try to kick the ball simultaneously.

Kicking the ball means to set its exact speed and a player can do that only if is close enough to the ball (`status.teamsStatus[team][player].distance` should be less than the value set in the match variable `INFLUENCEZONE`, except for the goalkeeper which i'll talk about later).  
To try kick the ball a player should set at least one between `ball.speedX` and `ball.speedY` in the `turn()` return object.  
If more than a player tries to kick the ball in the same frame, the successful kick will be selected at random between all of kick attempts, the closer a player is to the ball, the highest is the probability of his kick to succeed over the others.  
Regardless of how many try to kick the ball there will always be a probability of missing the ball.  
The ball's speed also decays over time if noone kicks it.  
If the ball is kicked against the border of the field, it will be simply bounced off it, as if there was a wall

Moving the players is a different story. A player cannot go from standing to his top speed in just a frame, nor can it invert his direction alltogether while mantaining full speed, that's why, for each turn a player can only modify his speed by a certain amount.  
To modify the speed of a player, you should set at least one between `player.deltaSpeedX` and `player.deltaSpeedY` in the `turn()` return object.  
If both of them are left 0, the speed of the player will remain the same as the previous frame.   
The maximum variation each player can apply to his speed each turn is defined in the match variable `MAXVARIATION`, while the maximum speed he can achieve is defined in the variable `MAXSPEED`  
If a player tries to move outside the field when he reach the border he shouldn't be able to continue in that direction.

### the Goalkeeper

as already stated, in both teams, player number 0 is the goalkeeper of the team. the goalkeeper works the same as any other player, but if close enough to his team's goal (on the x axis) he will be able to hit the ball from farther than the others and it will have more probability than the normal to be the one who kicks the ball.  
This means that if the goalkeeper X distance is less than a given value (you can find that in the `GOALKEPERLIMIT` variable), his  `INFLUENCEZONE` will be overridden by an higher number (stored in `GOALKEEPERINFZONE`), and his attempt to kick will be (at same distance from the others) more relevant than the others when choosing at random between all the kick attempts.



# Notes
this sections contains a set of rules and clarification which are not included in the Instructions section

### Initial positions
At the beginning of the game, the function init() is called one time for each instance of your player. it should return an array with an X and an Y coordinates which should be a valid starting position for that team.  
All the positions should be inside the playing field, so both the coordinates will have to be greater than 0, and smaller than the maximum size for that axis.  
Team0 should begin in the left side of the field, so the the X coordinate should comply with `x<MAXX/2`, on the other hand, the team1 starting X coordinate should be `x>MAXX/2` because they start on the right side of the field.
If you set your start position where you aren't allowed, your player will be moved to the closest valid starting point.

### Oblique speed
`MAXSPEED` and `MAXBALLSPEED` indicate respectively the maximum player speed and the maximum ball speed. This means when you set speedX and speedY value if 
<img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\sqrt{speedX^2&space;&plus;&space;speedY^2}&space;>&space;maxSpeed" title="sqrt(speedX^2 + speedY^2) > maxSpeed" />
your speed data will be scaled to keep the direction you want to set, but respecting the maxSpeed limit.
### Balancing
DevSoccer was just finished, and all the constants, that do balance the game, weren't yet tested with a proper team file, hence the game could be unbalanced.  
Once i get some feedback and some submissions i might balance them to make the game better.

# Conclusion
I hope you will have fun with DevSoccer. If this has somewhat popularity, i might add something where to share AIs. in the meantime, if you made an AI for DevSoccer i would be pretty happy if you shared it with me.
in that case you can find me at Telegram (@Protoh) or via email (dsfabio[at]live.it)