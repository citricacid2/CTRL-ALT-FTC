---
description: Re-opening the loop
---

# Feedforward Control

In the previous chapters, we focused on methods of feedback control. Feedback control is excellent for many systems and allows for effects such as disturbance rejection to improve the system's performance. While this approach is exemplary, often, we can increase performance even further with the addition of an open-loop feedforward controller.

‌ Feedforward works by essentially 'predicting' the mapping between plant inputs and outputs rather than iteratively converging on the desired result with a feedback controller.

‌ The advantage of feedforward / open-loop control, in general, is that it is immune to both time delay and sensor noise. This means that, unlike feedback control, bad sensor data or delays between inputs are much less likely to cause a system to be unstable.

Another great use for feedforward is to replace the integral term of a feedback controller.  Using feedback as an alternative to specifically the integral term is often a valid solution if one is worried about issues such as integral windup impacting the systems performance. &#x20;

### Software implementation of feedforward

```java
double output = K * reference;
```

As you can see, feedforward is farily trivial compared to the feedback control loops such as PID that we implemented in previous chapters.  This is because feedforward is acting almost as a unit conversion between some input and some output.  This conversion is determined by the tuned value K in the code snippet above.

In FTC this value is often referred to as 'Kv', specifically when dealing with controlling the velocity of a plant.  Additionally a term for acceleration, fittingly called 'Ka' can also be added.&#x20;

```java
double output = (Kv * velocityReference) + (Ka * accelerationReference);
```

&#x20;Using acceleration and velocity references in combination for our output allows our system to follow trajectories. Trajectories effectively tell our system at any given point in time what the system's states should be.

### Closing the loop

We would often like the benefits such as improved disturbance rejection that feedback control offers while keeping the stability that feedforward control provides. We can achieve this by coupling the outputs together.

Here we can see a proportional + feedforward controller for a velocity system:&#x20;

```java
while (setPointIsNotReached) {

    double output = Kp * (referenceSpeed - currentSpeed) + Kv * (referenceSpeed);

}
```

We can also use feedforward in combination with a full PID controller such as the one we developed in previous chapters, often this setup can be referred to as PIDF:

```java
while (setPointIsNotReached) {

    double output = PID(referenceSpeed, currentSpeed) + Kv * (referenceSpeed) + Ka * referenceAccel;

}
```

### Arm Feedforward

Rotating arms such as the one in the image below can be very easy to make but can sometimes make programming challenging.  This is because of the nonlinear dynamics caused by gravity as the arm rotates.  In order to compensate for these nonlinear dynamics we can use a gravity feedforward to linearize the system.

![FTC 8300 Ultimate Goal Robot with Rotating Arm](<.gitbook/assets/Screen Shot 2021-12-02 at 8.57.29 PM.png>)

In order to account for the gravity we can use the cosine of our target angle in order to compensate for the gravity.&#x20;

```java
double Kcos = SOME_VALUE_YOU_TUNE; 
double reference = YOUR_TARGET_ANGLE;
double power = Math.cos(reference) * Kcos; 
```

We can then use the code above in combination with a PID controller or other feedback controller.  Using this will improve the reliability as it will more accurately compensate gravity than a simple integrator (though we recommend using both this and an integrator for optimal results).&#x20;

This works because cos is the ratio of the adjacent of a right triangle divided by the adjacent of a triangle.  Whenever the arm is extended straight out (180 degrees) the cos is at its peak (1) as this is the point where it must use the most l torque to hold its weight, Whenever we want to go straight down (90 degrees) the arm does not require any torque to hold its weight, which matches our feedforward controller as the cosine of 90 degrees is 0. &#x20;

By using a nonlinear feedforward controller we can improve the reliability of our control system and improve the performance of our system.&#x20;

### Slide Gravity Feedforward





