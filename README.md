# ECSE275 Final Project - Manufacturing Line

## Team Members and Roles
* Endar Li - Team Lead, Robot Scene Creation, General README Writer, Proximity Sensors, Debugger, Data Analysis
* Jia Yang Lin - Gripper, End Point Path Planning
* Josh Cook - Inverse Kinematics, End Point Path Planning
* Luca Ciampaglia - Conveyor Belts, Proximity Sensors, README Writer, Debugger, End Point Path Planning
  
## Introduction
This project uses robot arms to move a box down conveyor belts to a goal point to emulate a manufacturing line process. Inverse Kinematics is explored to actualize movement in the arm. Proximity sensors are used both in the gripper to grab boxes, and as unique objects to detect boxes at the end of conveyors. Additionally, the gripper actuates using dynamics.
  
## Approach
### Robot Scene
A general setup of the project consists of two perpendicular conveyor belts, two stationary IRB140 robot arms and the end of each conveyor (grippers not included in the below image), a few cuboids, proximity sensors that detect the boxes and stop the conveyor, end effectors to assist robot arm movement, and a final goal point for where the cuboids end their journey.

<img width="330" alt="image" src="https://github.com/lc-st1/ECSE275/assets/53535721/350cc390-2faf-433f-a3bd-a08c3afcda8d">
  
### Robot Movement Method (Inverse Kinematics)
To move the joints of the robot arms, we implemented inverse kinematics. We calculated the first 3 joints using the same method used in Assignment 4 of the homework. From the homework, we used the elbow-down implementation for our arms as that is the most reasonable approach for picking up and moving boxes with a stationary arm. For the remaining 3 joints of the arm (that spins the wrist along the axis of the furthest joint from the base, rotates the wrist up and down, and spins the gripper connected to the arm), we referenced Mohammed Almaged's paper on "Forward and Inverse Kinematic Analysis and Validation of the ABB IRB 140 Industrial Robot" to implement inverse kinematics. The most helpful formulas from his paper were the ZYZ Euler's angles formulas as they were decently accurate when implemented into the robot. The angles were experimentally offset and modified to increase accuracy.

Below, are the angles:  
$θ_5 = atan^2( \sqrt{g_{31}^2 + g_{32}^2}, g_{33} ) $  
$θ_4 = atan^2( g_{32} / s_β, -g_{31} / s_β ) $  
$θ_6 = atan^2( g_{23} / s_β, g_{13} / s_β ) $  
  
### Detection Method (Proximity Sensor)
To control the movement of the conveyor belts, we chose to use ray-type proximity sensors. These were set up to detect when a cube passes by on the conveyor belt. Upon triggering, these set the target speed of the conveyor belt to 0 and initiate the robot arm movement. These had to be placed slightly before the end of each conveyor belt to allow for the declaration time lag. We also used a proximity sensor to trigger the movement of the second conveyor belt to perform the same aforementioned function. These sensors were chosen for their ease of use, as the sensing function in the main script could call each of the proximity sensors and control the system based on their output. Additionally, the cuboids only need to be detected to cross a threshold position so other sensors would be unnecessarily complex for our application.

A GIF of the proximity sensors is shown below:  
![prox](https://github.com/lc-st1/ECSE275/assets/53535721/be575905-8063-42d6-9385-b31d03ebb58f)
  
### Cube Retrieval Method (Gripper)
An RG2 gripper was used to interact with and pick up the cubes. This gripper design was chosen as it interfaced with the robot arm easily and came with standard fingers for gripping. These are controlled via a force input, velocity input, and a boolean control input. For this project, the force and velocity inputs were left as the default values. We then control the gripper via the boolean input, toggling the gripper to be either open or closed.

#### Alternatives Considered
##### Suction
https://github.com/lc-st1/ECSE275/assets/53535721/be6465fa-5585-4768-a906-7beb970e92b3

Initially, other methods of interaction were considered. The first was using a suction-based manipulator to pick up the cubes. However, we were unable to control the gripper fully and chose not to use it. In addition to this, we decided that the mechanical gripper was a more interesting challenge to implement.
  
### Path Planning Method (Follow Various Endpoints)
We looked into samples from CoppeliaSim with the same gripper to understand how to implement the path planning method, but due to the samples using IK environments and threading, we were unable to replicate their method. Instead, to actuate the grippers, we performed the following to grab and release the box(es):
1. Move towards the box.
2. Use sim.checkCollision() and check gripper to box distance to check that the grippers collided with and grabbed the box.
4. Move towards a designated endpoint.
5. Check that the distance between the box and the goal point is less than a designated threshold to release the box.
6. Repeat the above for the other arm and boxes.

As to not break the RobotScene file, we tested this implementation separately, first. A GIF of the test is shown below.  
![Gripper](https://github.com/lc-st1/ECSE275/assets/53535721/7ff98c32-a196-45bb-9d9f-ec99f35539a6)

#### Alternatives Considered
##### Grabbing Boxes Using A Parent
Before we could figure out a more sensible method of grabbing the box with our inverse kinematics method, we set the parent of the box to the arm to grab it and the parent to the conveyor to release it.
  
### Difficulties Encountered
* Offsets for IK
  * Looked at Almaged's paper for reference and played around with the values in the angle formulas using educated guesses based on knowledge from class.
* Knowing when the gripper touched/grabbed the box
  * Was very difficult and couldn't get the proximity sensor on the gripper to give us what we were looking for. Initially figured out how to make it work using the parent method, but ultimately found a more sane method which is step 2 of the path planning process.
* Knowing when to let go
  * Were not sure when to let go of boxes in the code.
  * Realized we could do this when the gripper reaches the next targeted endpoint (used the distance from the endpoint to the gripper as a flag).
* When to make arms go back to their original position
  * Once the arms drop a box, an index in the loop increases and the new target is set to the original pick-up position. Once it reaches that, the next cuboid is set to the new target.

  
## Results
A run of the manufacturing line as a GIF:  
![OGRunGIF](https://github.com/lc-st1/ECSE275/assets/53535721/1b078f7c-e99e-45b3-b1ec-bff14c13a1d4)  
The full-length YouTube video:  
[![](https://markdown-videos-api.jorgenkh.no/youtube/3nSXLGADSrU)](https://youtu.be/3nSXLGADSrU)

The fastest yet reliable speed that we could get from the manufacturing line was 0.04 on both conveyors. The resulting runtime had an average of 2:00 minutes and a 100% success rate for the 4 iterations of the speed combinations that we ran. The third conveyor was set to a speed of 0.3 but modifying did not do much to change the runtimes.

A notable run from our tests was from a speed combo of 0.06 on the first conveyor and 0.05 on the second. It was the fastest runtime of our tests at 1:47 minutes. We do not recommend this combo because it is highly unreliable. Our success rate from this combo was 20% of the 5 iterations that we ran of it but we suspect the true success rate is closer to 1% as the single success was due to a stroke of luck. For the other 4 iterations of this combo, the third and second boxes would get stacked on top of each other and the second arm would usually stop moving once it reached the third box due to dropping it when it moved the second to the endpoint. It would try to configure to the orientation of the dropped box but this maneuver would break it. Our one successful run of this speed combo likely happened because the third box dropped and landed on a convenient orientation that the robot arm could grab.

Displayed is a short version of our data analysis table. The full Google sheet is linked under the condensed table.

| Iter | Conv1 (v) | Conv1 (v) | Works? | Runtime |
|------|-----------|-----------|--------|---------|
| 0    | 0.03      | 0.03      | yes    | 2:06:47 |
| 1    | 0.05      | 0.05      | no     |         |
| 2    | 0.05      | 0.06      | no     |         |
| 3    | 0.07      | 0.05      | no     |         |
| 4    | 0.06      | 0.05      | no     |         |
| 5    | 0.06      | 0.05      | yes    | 1:47:05 |

Link to our sheet: https://docs.google.com/spreadsheets/d/1fs-ZvYQxiL3QqOqIIaE0Rxxe_IdVqTtStlsILmmjwpo/edit#gid=731419285
  
## Conclusion
The manufacturing line transports a box down two different conveyor belts to an endpoint (where it drops onto a third conveyor and gets chucked off the map) using two IRB140 arms that move using inverse kinematics and by following different endpoints. This project can be further developed by implementing a more scalable and versatile path-planning method. Such a method can be used to make it easier for the project to be used for other applications and increase in complexity. Some examples of increasing the complexity—besides optimizing for speed—are adding more conveyor belts, robot arms, and/or boxes. An example of using this for another application could be using the robot arms to put objects in empty boxes before moving the boxes.

## Instructions to Use
1. Clone the repository: <br>
  <code>git clone git@github.com:lc-st1/ECSE275.git</code>
2. Open the RobotScene.ttt file and run it
