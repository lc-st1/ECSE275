# ECSE275 Final Project - Manufacturing Line

## Team Members and Roles
* Endar Li - Team Lead, Robot Scene Creation, General README Writer, Proximity Sensors
* Jia Yang Lin - Gripper, End Point Path Planning
* Josh Cook - Inverse Kinematics, End Point Path Planning
* Luca Ciampaglia - Proximity Sensors, README Writer

## Description
Use robot arms to move a box down conveyor belts to a goal point.

## Instructions to Use
1. Clone the repository: <br>
  <code>git clone git@github.com:lc-st1/ECSE275.git</code>
2. Open the RobotScene.ttt file and run it


## Robot Scene
A general setup of the project consists of two perpendicular conveyor belts, two stationary IRB140 robot arms and the end of each conveyor (grippers not included in the below image), a few cuboids OR a cuboid, proximity sensors that detect the box(es) and stop the conveyor, end effectors to assist robot arm movement, and a final goal point for where the cuboid will stop.

<img width="330" alt="image" src="https://github.com/lc-st1/ECSE275/assets/53535721/350cc390-2faf-433f-a3bd-a08c3afcda8d">

## Robot Movement Method (Inverse Kinematics)
To move the joints of the robot arms, we implemented inverse kinematics. (PLEASE CHECK IF CORRECT, JOSH) We calculated the first 3 joints using the same method used in Assignment 4 of the homework. We used the elbow-down implementation for our arms as that is the most 

## Detection Method (Proximity Sensor)
...

## Cube Retrieval Method (Gripper)
...

### Alternatives Considered
#### Suction
https://github.com/lc-st1/ECSE275/assets/75393058/4b42e3b2-67f5-425b-818a-d7759173aaff



## Path Planning Method (Follow Various Endpoints)
...
