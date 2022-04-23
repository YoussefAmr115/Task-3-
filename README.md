### Inputs used:
- **no_obstacles:** number of obstacles
- **initpos[0]:** initial position x for the robot
- **initpos[1]:** initial position y for the robot
- **initpos[2]:** initial theta 
- **targetpos[0]:** initial position x for the target
- **targetpos[1]:** initial position y for the target

### Outputs generated:
- map display showing the robot, obstacles and the target
- targets placed in coordinates each time the code runs


## Code function:
The main purpose of the code is to display the simulation of a robot with a target both positions set by the user when is asked, the user also determines the number obstacles placed randomly on the map , the target that is set must be reached by the robot without colliding with any of the obstacles that are set.


## Flowchart


## supporting files (robot image, map with obstacles, target and the robot):
![image](https://user-images.githubusercontent.com/103934864/164878302-35250bac-3498-4e97-83ad-dea3481c71ed.png) 
![image](https://user-images.githubusercontent.com/103934864/164892142-85182813-a515-45dc-a763-6346058f9641.png)



## Further improvments
- The robot needs to detect if there is obstacles on the starting point or the target
- The robot needs to improve creating new paths in a more advanced way when meeting obstacles

## Code:

from roboticstoolbox import Bicycle, RandomPath, VehicleIcon, RangeBearingSensor,LandmarkMap
from math import pi , atan2
import matplotlib.pyplot as plt


anim = VehicleIcon('robot.png', scale = 2)

#storing initials and positions
initpos = [0, 0, 0];
targetpos = [None]*2

#getting the target positions inputs
targetpos[0] = float(input("Enter target x co-ordinate: "))
targetpos[1] = float(input("Enter target y co-ordinate: "))

#getting the initial position for the robot
initpos[0] = float(input("Enter initial x co-ordinate: "))
initpos[1] = float(input("Enter initial y co-ordinate: "))
initpos[2] = float(input("Enter initial theta: "))

#receiving the number of obstacles fome the user
no_obstacles = int(input("Enter the number of obstacles: "))

#creating an instance for the ronbot which is the bicycle 
veh = Bicycle(
animation = anim,
control = RandomPath,
dim = 10,
x0=initpos,
)

veh.init(plot=True)
veh._animation.update(veh.x)

#putting the mark on the target position on the map
goal = targetpos
target_marker_style = {
    "marker": "D",
    "markersize" : "6",
    "color" : "g",
}
plt.plot(targetpos[0], targetpos[1], **target_marker_style)

#generating obstacles on the map
map = LandmarkMap(no_obstacles, 20)
map.plot()

sensor = RangeBearingSensor(robot = veh, map = map ,animate=True)
sensor_readings = sensor.h(veh.x)
print(sensor_readings)

run = True
while(run):
    if((abs(goal[0]-veh.x[0]) >0.6) or (abs(goal[1]-veh.x[1]) > 0.6)):
        run=True
    else:
        run=False
    for i in sensor_readings:
        if (i[0] < 3):
            if(abs(i[1]) < pi/4):
                run = False
        else:
            target_heading = atan2(
                targetpos[1] - veh.x[1],
                targetpos[0] - veh.x[0]
            )
            steer = target_heading-veh.x[2]
            if steer > pi:
                steer = steer-2*pi
            veh.step(0.8, steer)
            if((abs(targetpos[0]-veh.x[0]) > 0.3) or (abs(targetpos[1]-veh.x[1]) > 0.3)):
                run = True
            else:
                run = False
            
            veh._animation.update(veh.x)
            plt.pause(0.005)

plt.pause(100)
