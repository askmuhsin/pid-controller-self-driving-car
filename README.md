# Project Intro :
This project implements a PID controller for steadily controlling the steering angle and throttle speed of a vehicle by observing the Cross Track Error(CTE). CTE is the lateral distance between the center of the vehicle and the track.
<img src="https://github.com/askmuhsin/pid-controller-self-driving-car/blob/master/images/CTE.jpg" width="256" height="200" title="CTE">

---
## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

----
## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
----    
## PID Implementation
A propotional-integral-derivative controller is perhaps the most widely used feedback control loop mechanism, mostly due to its ease of implementation and also for being computationaly efficient.
<br></br><img src="https://github.com/askmuhsin/pid-controller-self-driving-car/blob/master/images/screenshot.png" width="350" height="200" title="PID">

## Parameter tuning
In this project there are two PID controllers, one for controlling the steering and the other one controlling the throttle.
The Control Gains for each of them are as follows:

| Gain       | Steering | Throttle |
|------------|----------|----------|
| Kp         | -0.15    | -0.5     |
| Ki         | -0.00001 | 0        |  
| Kd         | -3       | 0        |  

These values have been obtained mainly by trial and error. But without an underlying intution of the PID control parameters, the combinatorial effort to figure out the Gains would be difficult. By defining a cost function to determine the performance of the PID values, it would be possible to implement techniques like twiddle. But here I have not made an attempt to do so, maybe its something worth trying as a future improvement.

### Steering Controller
TL;DR  
Kp --> How hard to steer  
Ki --> To overcome systematic Bias  
Kd --> Reduce Oscillation  

Here the basic idea was to maintain two of the gains, constant and try to see how the system responds to the changes in one of them. For example I started by setting Ki & Kd to 0 and tried values from -5 to 5 in steps of 1 for Kp. It was obvious that the response to error had to be directional so Propotional gain had to be negative. The vehicle started to respond to the CTE, but soon began oscillating widely. So a higher value for Kd was set. A slight alignment issue was observed over time, inorder to counter that a small Ki was also assigned.

### Throttle Controller
Implementing throttle controller was much easier. The prinicple was simply to slow down the vehicle at turns and accelerate on straight road. So for error I took the absolute value of CTE (as the side to which the lateral displacement was not a factor of importance for the throttle value). And a Bias (+0.55) term was added to map the throttle values accordingly.
Line 93:main.cpp `throttle_value = 0.55 + pid_throttle.TotalError();`. A control statement was added to maintian the speed in a predefined range.

---
## Result
The make files run without any errors. The car completes one lap of the track without leaving the drivable portion of the surface.  
[Video-One-Full-Lap](https://youtu.be/SvE5Mc0OZpA)   
![project_4](https://github.com/askmuhsin/pid-controller-self-driving-car/blob/master/images/project_4.gif)
