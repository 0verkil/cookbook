# Pose Exponential Odometry from Scratch

*TODO: Make this page look nice and more friendly.*

Have you ever wondered how Roadrunner and other libraries track your robot? Have you ever wanted to make your own localization system, but you didn't know how? Have you ever gotten your brain fried after reading the odometry section of *Controls Engineering in the FIRST Robotics Challenge* and/or the odometry section of *Mobile Robot Kinematics for FTC?* Then this guide is for you.

This guide seeks to make the math and intuition behind pose exponential odometry (typically the most advanced form of odometry used in FTC) more accessible to readers who do not know, or are not comfortable with calculus, as well as to provide a more intuitive explanation to those who are.

## Ingredients

1. A math background including circle geometry (only the basics) and unit circle trigonometry
2. A willingness to learn!

*Some of these sections will be fairly complex. Although I will do my best to ensure that the sections are as digestable as possible, and I will introduce any terminology not outlined in the Ingredients before using it, I cannot guaruntee this will be an easy read. Don't be afraid to whip out a pen and paper and actually work through the math yourself!*

## The Recipe

### Definitions

Before we start, let's get a bit of background. If you've ever read Game Manual 2, this part will look familiar to you.

*Localization* is the ability to track your robot's position, regardless of what method you use.
*Odometry* specifically refers to the use of sensors to track the **change in robot position**, which is done repeatedly to estimate the robot's actual position.

A single instance of finding the change in robot position and updating the estimated robot position will be called an *update*.

When we say *delta x*, we mean the change in x since the last update. That is, delta x (or 𝚫x) = current x - last x.

// TODO: add images for this section
The *coordinate frame* refers to the coordinates we use to actually track the robot's position. There are actually two important *coordinate franes*, so let's look at them independently:
- The *local coordinate frame* refers to the *coordinate frame* where the **robot** is always at the point (0, 0). In this coordinate system, positive x is forward for the robot and positive y is to the left. Coordinates in the *local coordinate frame* will be denoted with a superscripted R for robot.
- The *global coordinate frame* refers to the *coordinate frame* where the **center of the field** is always at the point (0, 0). In this coordinate system, positive x is towards the backdrops and positive y is towards blue. Coordinates in the *global coordinate frame* will be denoted with a superscripted G for global.

The *center of rotation* for a robot is the point on the robot that does not move while rotating. Typically, this will be a point that is roughly the average of all your wheel positions; however this is not always the case. The center of rotation is actually the point you are tracking: when you say the robot is at (0, 0), it is really the center of rotation that is at (0, 0). For more info, see [this](https://micromouseonline.com/2017/06/12/rotation-centre-for-your-robot/#:~:text=The%20rotation%20centre%20is%20the,the%20robot%20out%20of%20position.) article from the micromouse community.

// add dead wheels and motor encoders to this section

### What is Odometry, Actually?

Odometry in FTC is often though of as one big magical thing, but it's actually split into two parts that work completely seperately! 

The first half of odometry takes in the encoder data and converts it into velocities: how fast your robot was moving in the x and y directions in the *local coordinate frame*. This step also finds the change in heading since the last time the odometry was updated. This part of odometry is what changes between drive motor encoder odometry, 2 dead wheel odometry, and 3 dead wheel odometry.

The second half of odometry takes the velocities and the change in heading and uses that to estimate the change in robot position in the *global coordinate frame*. This part of odometry is where pose exponential odometry comes in, and the main focus for this recipe.

### Dead Wheel Configurations

We will start with the assumption that we are using 2 dead wheels and the IMU as our odometry system (commonly referred to as 2 wheel odometry), as this is both easy to explain and builds the necessary intuition for all other potential systems.

There are a lot of valid dead wheel configurations that rely on ideas from linear algebra to solve for the desired velocities. However, linear algebra is hard and this is unnecessary for most teams, so we will be proceeding by assuming that one of our dead wheels is pointing forwards and backwards (parallel to x<sup>G</sup>), and the other dead wheel is pointed side to side (parallel to y<sup>G</sup>).

Luckily, this makes our math really easy! We have exactly 1 sensor for heading (the IMU), 1 sensor for x<sup>G</sup>, and 1 sensor for y<sup>G</sup>. If we move without rotating, the change in ticks in our dead wheel encoders should almost exactly match the amount we moved. 

### Ticks to Inches

I see a lot of people getting confused about what a tick actually is. Ticks are not a measure of distance; they are a measure of rotation, just like a degree or a radian. The ticks per revolution, counts per revolution, and/or pulses per revolution refer to **how many parts a full revolution is split into**. For example, if there were 100 ticks in a revolution, each tick would be 1/100th of a revolution, which would be 360/100 = 3.6 degrees. That is, **a motor moving 1 tick would be equivalent to it rotating 3.6 degrees**. 

The usefulness of ticks comes from the fact that wheels typically have a (roughly) constant radius, meaning we can use the arc length formula for circles to figure out how far the wheel has moved based on how far has rotated. This formula is very important and will come up many times again!

> Arclength = radius * angle (in radians)
// insert image

For wheels, this formula looks like this:

> 𝚫Distance = Wheel radius * 𝚫Ticks * Radians per tick

However, typically ticks are reported in **ticks per revolution**, not **radians per tick**. Since there are 2𝛑 radians in a single revolution, we can rearrange the equation as follows:

> 𝚫Distance = Wheel radius * 𝚫Ticks * Revolutions per tick / 2𝛑
> 𝚫Distance = Wheel radius * 𝚫Ticks / 2𝛑 * Ticks per revolution

And that's our conversion formula! Whatever units the wheel radius is in, the distance will be in (typically inches). You'll also notice that we have these three constants (2𝛑, ticks per revolution, and wheel radius). Some people choose to combine these constants into one big constant:

> 𝚫Distance = 𝚫Ticks * Inches per tick

Or potentially:

> 𝚫Distance = 𝚫Ticks / Ticks per inch

These are all theoretically equivalent, but I would suggest using the latter for your actual implementation, and keeping in mind the math from the first one, since the latter one gives you less places to go wrong and only one constant to tune (If you're familiar with Roadrunner, one of the major changes between the old 0.5.6 version and the new 1.0.x versions was the shift to the single-constant version, which people have reported makes it much easier and faster to tune).

### Accounting for Rotation

If we could put both of our dead wheels on the center of rotation, we would be done now! In reality, however, we usually cannot even put **one** of our dead wheels directly on the center of rotation. This will cause the dead wheels 

*Last Updated: 2024-02-14*