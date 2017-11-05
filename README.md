# SDC-Path-Planning-P11

## Path Planning
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

In this project, I built a path planner that creates smooth, safe trajectories for an autonomous car to follow in a highway. The highway track has other vehicles, all going different speeds, but approximately obeying the 50 MPH speed limit.

## Overview 

### Goals
In this project, the goal was to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. The car should tries to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car avoids hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car is able to make a complete loop around the 6946m highway. Since the car is trying to go ~50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car does not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

#### The map of the highway is in data/highway_map.txt
Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes from 0 to 6945.554.

### Generating the Path

#### Going Around The Track
First, I generate five points in Frenet coordinates that I want the path to pass through. The first two points represent the last two points in the previous path (which we'll include as the first section of the new path). The next three points are the points in our target lane (constant d) spaced 30m apart. Then, I generate a spline through those points using the `spline.h` library and construct points from the first 30m of the spline as the path. Finally, I add those points on the spline (the newly generated path points) to `next_x_vals` and `next_y_vals`.

#### Keep Lane
The `d` value is used for the path constant. I set `d = 2 + 4 * lane`, since we know that a lane is 4m wide and we want to stay in the center of the lane.

#### Respecting The Speed Limit
A variable called `speed_limit` determines the maximum speed the car will drive - the variable is set to `49.5` so it is slightly lower than the speed limit. The `ref_velocity` is the value that actually moves the car, never going beyond the `speed_limit` value. When I add points to the path, I make sure to keep a distance of `0.02 * ref_velocity / 2.24` between each point. One poin to notice is, even ignoring the speed limit, the car still behaves pretty well, avoiding most of the collisions.

#### Minimum Jerk
I gradually increase the vehicle velocity (at a `0.224` rate) until it reaches the `speed_limit` variable value, so the jerk is minimized.

#### Avoiding Collisions
First, I check if the car is close - behind - to another car in its lane. If that is the case, I gradually decrease the car's velocity (at a `0.224` rate) to avoid a colision. This is done by using the data from the `sensor_fusion` vector to predict where each other car will be in the next timestep. I use the Frenet coordinates to determine whether other cars are close to our car (`s` coordinate) and which lane they are in (`d` coordinate).

#### Lane Changes
I first check if the car is close to the car in front on the same lane - see above. Then, I determine if a lane change is safe by checking if the car in a lane is close to us in the `s` direction - if that car is too close, a change is not safe. If a change is safe, I update the `lane` variable from the current lane to the target lane.

## Video with Results

A video of the results can be found [here](https://youtu.be/nFgkeNzCuGU).

## Sources and Credits
This is a student project with the primary goal being to learn and understand the concepts behind a model predictive control. 

1. Udacity provided starter code, quiz code and lessons.
2. Ideas from other students and mentors through forums, chat channel and github.
3. The various dependencies libraries.
