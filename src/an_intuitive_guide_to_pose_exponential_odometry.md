# An Intuitive Guide to Pose Exponential Odometry

Have you ever wondered how Roadrunner and other libraries track your robot? Have you ever wanted to make your own localization system, but you didn't know how? Have you ever gotten your brain fried after reading the odometry section of *Controls Engineering in the FIRST Robotics Challenge* and/or the odometry section of *Mobile Robot Kinematics for FTC?* Then this guide is for you.

This guide seeks to make the math and intuition behind pose exponential odometry (typically the most advanced form of odometry used in FTC) more accessible to readers who do not know, or are not comfortable with calculus, as well as to provide a more intuitive explanation to those who are.

## Ingredients

1. A math background including circle geometry (only the basics) and unit circle trigonometry
2. A willingness to learn!

*Some of these sections will be fairly complex. Although I will do my best to ensure that the sections are as digestable as possible, and I will introduce any terminology not outlined in the Ingredients before using it, I cannot guaruntee this will be an easy read. Don't be afraid to whip out a pen and paper and actually work through the math yourself!*

## The Reciple

### Definitions

Before we start, let's get a bit of background. If you've ever read Game Manual 2, this part will look familiar to you.

*Localization* is the ability to track your robot's position, regardless of what method you use.
*Odometry* specifically refers to the use of sensors to track the **change in robot position**, which is done repeatedly to estimate the robot's actual position.

A single instance of finding the change in robot position and updating the estimated robot position will be called an *update*.

// consider removing for the sake of readability
The *delta* of a variable refers to the change in that variable since the last time we observed it. Typically, this comes up in the context of *encoder deltas* (the difference between the current encoder position and the encoder position at the last *update*).

The *coordinate frame* refers to the coordinates we use to actually track the robot's position. There are actually two important *coordinate franes*, so let's look at them independently:
    - The *local coordinate frame* refers to the *coordinate frame* where the **robot** is always at the point (0, 0). In this coordinate system, positive x is forward for the robot and positive y is to the left.
    - The *global coordinate frame* refers to the *coordinate frame* where the **center of the field** is always at the point (0, 0). In this coordinate system, positive x is towards the backdrops and positive y is towards blue.

### What is Odometry, Actually?

Odometry in FTC is often though of as one big magical thing, but it's actually split into two parts that work completely seperately! 

The first half of odometry takes in the encoder data and converts it into velocities: how fast your robot was moving in the x and y directions in the *local coordinate frame*. This step also finds the *delta heading*, the change in heading since the last time the odometry was updated. This part of odometry is what changes between drive motor encoder odometry, 2 dead wheel odometry, and 3 dead wheel odometry.

The second half of odometry takes the velocities and the *delta heading* and



*Last Updated: 2024-02-14*