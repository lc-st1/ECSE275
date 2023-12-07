# ECSE275 Final Project - Manufacturing Line

## Team Members and Roles
* Endar Li - Team Lead, Robot Scene Creation, General README Writer, Proximity Sensors
* Jia Yang Lin - Gripper, End Point Path Planning
* Josh Cook - Inverse Kinematics, End Point Path Planning
* Luca Ciampaglia - Conveyor Belts, Proximity Sensors, README Writer

## Introduction
This project uses robot arms to move a box down conveyor belts to a goal point to emulate a manufacturing line process. Inverse Kinematics is explored to actualize movement in the arm and proximity sensors are used both in the gripper to grab boxes and as unique objects to detect boxes at the end of conveyors. Additionally, the gripper actuates using dynamics.

## Approach
### Robot Scene
A general setup of the project consists of two perpendicular conveyor belts, two stationary IRB140 robot arms and the end of each conveyor (grippers not included in the below image), a few cuboids OR a cuboid, proximity sensors that detect the box(es) and stop the conveyor, end effectors to assist robot arm movement, and a final goal point for where the cuboid will stop.

<img width="330" alt="image" src="https://github.com/lc-st1/ECSE275/assets/53535721/350cc390-2faf-433f-a3bd-a08c3afcda8d">

### Robot Movement Method (Inverse Kinematics)
To move the joints of the robot arms, we implemented inverse kinematics. (PLEASE CHECK IF CORRECT, JOSH) We calculated the first 3 joints using the same method used in Assignment 4 of the homework. From the homework, we used the elbow-down implementation for our arms as that is the most reasonable approach for picking up and moving boxes with a stationary arm. For the remaining 3 joints of the arm (that spins the wrist along the axis of the furthest joint from the base, rotates the wrist up and down, and spins the gripper connected to the arm), we referenced Mohammed Almaged's paper on "Forward and Inverse Kinematic Analysis and Validation of the ABB IRB 140 Industrial Robot" to implement inverse kinematics. The most helpful formulas from his paper were the ZYZ Euler's angles formulas as they were quite accurate when implemented into the robot.

Below, are the angles:  
$θ_5 = atan^2( \sqrt{g_{31}^2 + g_{32}^2}, g_{33} ) $  
$θ_4 = atan^2( g_{32} / s_β, -g_{31} / s_β ) $  
$θ_6 = atan^2( g_{23} / s_β, g_{13} / s_β ) $  

### Detection Method (Proximity Sensor)
To control the movement of the conveyor belts, we chose to use ray type proximity sensors. These were set up to detect when a cube passed by on the conveyor belt. Upon triggering, these would set the target speed of the conveyor belt to 0 and initiate the robot arm movement. These had to be placed slightly before the end of each conveyor belt to allow for the declaration time. We also used a proximity sensor to trigger the movement of the second conveyor belt, setting the target speed to 0.5 once a cube is detected in front of it. These were chosen for their ease of use, as the sensing function in the main script could call each of the proximity sensors and control the system based on their output.

### Cube Retrieval Method (Gripper)
An RG2 gripper was used to interact with and pick up the cubes. This gripper design was chosen as it interfaced with the robot arm easily and came with standard fingers for gripping. These are controlled via a force input, velocity input, and a boolean control input. For this project, the force and velocity inputs were left as the default values. We then control the gripper via the boolean input, toggling the gripper to be either open or closed.

#### Alternatives Considered
##### Suction
https://github.com/lc-st1/ECSE275/assets/75393058/4b42e3b2-67f5-425b-818a-d7759173aaff

Initially, other methods of interaction were considered. The first was using a suction-based manipulator to pick up the cubes. However, we were unable to control the gripper fully and chose not to use it. In addition to this, we decided that the mechanical gripper was more interesting challenge to use.
 


### Path Planning Method (Follow Various Endpoints)
...

## Results
it dont work

## Conclusion
...

## Instructions to Use
1. Clone the repository: <br>
  <code>git clone git@github.com:lc-st1/ECSE275.git</code>
2. Open the RobotScene.ttt file and run it
