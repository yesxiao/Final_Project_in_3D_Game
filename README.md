# Final Project in CPSC 6820-3D Game Programming
### Team Name: Refulgence
### Team Member: [Zhongyue Ren](https://github.com/zhongyr),  [Wufangjie Ma](https://github.com/mwfj)

### Our website
[website](https://mwfj.github.io/Final_Project_in_3D_Game/ "website")

For our lastest update, see this link: [update](https://mwfj.github.io/Final_Project_in_3D_Game/update "update")

## Our Final playable versioin:

### Please scroll down the page to view the control information and game mechanics

[Final Playable version](http://wufangm.people.clemson.edu/Final_playable_version/index.html)

 **Note that: Since our game map is dynamically generated, it will still take some time to generate the game map at the beginning of the game**

Video Demo: https://drive.google.com/file/d/12-4LReisw6tiSoZFlJ_Y-dx6W6bKyRKv/view?usp=sharing

> Screenshot for our First Playable Version
![screen_shot_fpv](readme_img/fpv_screenshot.png)

> Screenshot for our SecondPlayable Version
![screen_shot_spv](update/pic/new_texture_preview.png)

>Navmesh and maze dynamically_generation demo
![navmesh_maze_dynamically_generation](readme_img/navmesh_maze_dynamically_generation.gif)

> Fight System<br>
![Fight_System](readme_img/Attack_system.gif)

> Boss Room <br>
![Boss_Room](readme_img/Boss_Room.gif)

## Import Assert
### If you want to import assert, please note that the scene name we use call **"Main"**.
### Then if you want to make runtime agnet work, plase manually change the NavMesh Agent setting and NavMesh Bake setting in the "Navigation" Window(Figure below)

>![Configure_navmash_agent](update/pic/Configure_navmash_agent.png)
>![Configure_navmesh_bake](update/pic/Configure_navmesh_bake.png)

### *More functional update please see the update page*

##  Character Control


1. A/D for Horizontal Move

2. W/S for Vertical Move

3. Mouse Left Button for Attack(click/Hold)

4. Hold mouse right button to change direction

5. Space to jump

6. Escape to quit the Game

7.  LeftShift to move down/resume the camera angle

8. Press F to turn on the switch

9. Scroll the mouse wheel to change the field of view


## Game Mechanics

The player first need to find three switches in the randomly generated maze.
When all the switches turned on, the boss room will opened. Then the game target will change to find the entry of boss room.
When the player enter the boss room, the boss will generate in the boss room. Now the game target is to kill the boss.
When the boss die, game over.



## Animation Control


1. Blend Tree for different direction running(Animation/PlayerAnimationController)

2. Different attack stage different animation(Animation/PlayerAnimationController)

   Use trigger condition to change the phase of attack animation. Using
   a cooldown to make sure the animation will not be interrupted. Also
   use onStateEnter and onStateExit to change the parameter isAttacking,
   which is related to the damage count.

3. Jump animation(Animation/PlayerAnimationController)

   Its difficult to synchronize the movement of the character with the  animation. The change from jump up animation to jump down should test   a lot for the best performance.

4. Dying animation

   Use any state -> dying. The transition condition is the bool Alive,
   which will be updated by the Character Stats scripts. Use onStateExit
   () event to destroy the dead object.

5. For Trash/Boss, we use animation transition to control different animation(idle, walk, getHit and attack; locate at  Trash: Animation/TrashAnimationController 	Boss: Animation/BossAnimationController)
   Both trash and boss we use StateMachineBehavior Script(dyingBehavior) to achieve the dying effect


### Boss Generate Animation

Boss generate generation is little different with trash. The reason is that when the boss generated, it will play an introduce animation first, where the boss is undefeatable  at that time. What's more, when boss playing the introduce animation, it will not chase main character even it has detect the exist of main character. After playing the introduce animation, it will start to chase main character just like other navmesh agent. I use AnimatorStateInfo to achieve this funciton



## Health Bar


Add world space canvas to the enemy prefab, and change the fill account
by the health point of the enemy.

If health equals 0, play the dying animation and destroy the object
after the animation.

And the boss health bar will use the camera space, and is located at the
top left of the screen. It will be activated when the boss is generated.

If we just set the canvas as a sub object of the enemy, the health bar
will rotate with the enemy. We want it always face to our camera, so we
add a script to dynamically detect the rotation and make sure the health
bar is perfectly displayed.



## Random Map Generation

Using a BFS based searching method. Each generation step randomly choose
one direction to add a new moveable cell or a wall to block the passage.

Trace the number of wall of one cell, make sure each cell should not be
isolated.



## Attack


### Detection

We use collision detection on the weapon model to check the attack. If
the weapon hits the enemy, it will have an collision event.

Add collision trigger on the weapon. Using onTriggerStay to check the
attack the collision for the enemy with a delay have the best
performance. Because sometimes enter and leave will not occur at the
same time as the animation event change the attacking stats. So trigger
the attack count and then delay the next attack until current animation
is finish will be the best.



### Hit Box

We should add additional box collider to check the attack. Because of
the NaveMesh, some times the Weapon may not really attack on the enemy
model, so a little bigger hit box should be added. This will improve the
use experience.



## Dynamic Navmesh Generation
Because all the object in our game is dynamically generated, traditional NavMesh creation is not fit for our game. Thus, I use dynamic way to create Navmesh. Specifically,  I use the NavMesh Component provided by Unity to achieve this.(link: https://github.com/Unity-Technologies/NavMeshComponents). In our game, the navmesh baker just like other Prefab. When the game open, the game map(maze) randomly generated and the NavMesh will according to the current map to bake the mesh

For NavMesh Agent, we have two types of navmesh agent in our game: one is Trash(walk around in the maze) and Boss(born in Boss Room when all the switch is open and main character enter the boss room). All of them will regard the main character as the target and chase it. When agent close to the main character, they will attack main character automatically. When agent and main character move further, agent start to chase main character again

Note that, there totally have three different trash type in the trash pool(trash_type_list). When the game starts, the system will randomly select three of these three types of monsters as the trash in the game, where in different game, the trash are different.

### Boss Creation

The boss creation is little complicated, when the main character turn all the switch on(3 in this game), boss room will open, and the location of door will automatically create a box collider to detect whether the main character enter the boss room or not. When the main character first enter the boss room, it will trigger this collider and boss will generate in the boss room.

## Sound

There has two background sound in the game, when the main character in the maze, the game will play relatively soothing background music. 

However, when the main character entered the boss room, the concert became relatively intense in order to give the player a sense of tension. 

Moreover, when boss room opened, it will have sound effect to describe it. 

When the main character kill boss, the background music will change to victory music.


### Character/Boss sound effect

Because the time limit, I only added sound effects for the main character and boss. When player run, attack and jump, it will have sound effect. When boss roar(introduce animation) and attack, it will have sound effect. We will add sound effect for all character in the future.


## Assert Source

[MainCharacter] https://haonstore.wixsite.com/main 

[FREE Stylized PBR Textures Pack] https://assetstore.unity.com/packages/2d/textures-materials/free-stylized-pbr-textures-pack-111778

[Trash] https://assetstore.unity.com/packages/3d/characters/humanoids/mini-legion-rock-golem-pbr-hp-polyart-94707

[Boss] https://assetstore.unity.com/packages/3d/characters/humanoids/golem-3620

[Maze music] https://opengameart.org/content/soliloquy

[Boss Room Music] https://opengameart.org/content/the-dark-amulet-dark-mage-theme 

[Victory Music] https://opengameart.org/content/lively-meadow-victory-fanfare-and-song

[Monster Roar] http://soundbible.com/1963-Giant-Monster.html

[Boss Punch] http://soundbible.com/992-Right-Cross.html

[Main character sound] https://assetstore.unity.com/packages/3d/characters/unity-chan-model-18705

[Open the door sound effect] https://opengameart.org/content/door-open-door-close-set



## [Final Proejct Proposal](https://mwfj.github.io/Final_Project_in_3D_Game/Final_Project_Proposal.pdf)


### What kind of game?

We would like to develop a 3d roguelike game. Players will take adventure in a randomly generated dungeon. The dungeon will have different rooms with different mechanism to unlock the next level. Different creeps which will drop random item, skills and equipment. It will much like a combination of *The Binding of Isaac* and *Risk of Rain 2*. The action mode in the risk of rain will provide stronger hitting feelings. The random room skill and boss generation is the key feature in roguelike game. 

![isaac_img](readme_img/Isaac.jpg)

> Screenshot of *The Binding of Isaac* from zhaodanji.com

![Risk_the_Rain_2](readme_img/risk_of_the_rain.jpg)

> Screenshot of *Risk of Rain 2* from gamesky.com

### The Control and UI

We develop the game for PC and Mac OS platform and player will control the avatar with keyboard and mouse. The control system will much like prevalent action game, using mouse to control direction and basic attack, using keyboard to spell abilities and move. The game UI will show the HP and some more character information related to the game mechanism, we will  make it detail in later update.

### System to push player forward

We don't want players farm creeps a lot and make the game mechanism have no challenge. So we want to develop a system to push player forward. It may related to the time like the blood moon mechanism in *Zelda: Breath of the Wild*. Typically the creeps and bosses will become more strong and fierce as the time passes. But I think it is not the best solution to balance the progress and farm parts of the game. So after finish main game mechanisms we will consider it again and choose one we preferred.

### Scripts and assets

Since I (Zhongyue Ren) have implemented the base of random dungeon generation algorithm in maze game, we will focus on the scene build scripts. It will related to the game system, different kind of room, different kind of creeps and bosses. And the specific movement of creeps and bosses, we will develop it together, each person responsible for some creeps and bosses I think, and also responsible for the animation of the corresponding objects.  **We have not decided to use any third-party assert now**. Once decide to use it, we will update the information on our website

### Plan

1. First release: we will implement the random dungeon and the movement of the main character, attack spell and other movement. So the player at this phase can just do a trip in a virtual world. And we will find some art resource to help us make the world more fascinating. We may add some attackable target to test the data for damage and HP balance, but it may not have corresponding art resource like hit animation. It will depends on the workload and whether we can find proper resources on the internet.
2. Second release: we will add more creeps, skills and equipment. Also at this phase, a roguelike game with its main function will be finished. Item dropping, random rooms and battle with creeps and bosses. Since time is limited, we may design only one boss battle that provide challenges for players but not just farm and push. 

### Meeting time

We have online meetings every two-three days to confirm the progress through Zoom. 

