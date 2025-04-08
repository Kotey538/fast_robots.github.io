---
title: "MAE 4910 Fast Robots"
description: "Lab 8: Stunts"
layout: default
---

# Lab 8: Stunts

In Lab 8, I integrated all the hardware and software developed throughout the course to execute a high-speed stunt with my RC car. Given the choice between a flip and a drift, I chose the flip stunt. In this task, the robot accelerates rapidly toward a wall and, upon reaching a specific distance, performs a front flip and continues moving in reverse.

* * *
 
## Prelab
Having decided to pursue the flip stunt, I learned the general methodology for executing a flip with an RC car. The key principle involves accelerating quickly with the mass distributed toward the front of the car, then either applying a sudden brake or quickly reversing direction to initiate the flip. 

## Stunt
To explore both approaches, I implemented simple test commands to evaluate how effectively each method triggered a successful flip. 

### Braking
```c
case FLIP_B:  {
    
    float u_0;

    // Extract the next value from the command string as an integer
    success = robot_cmd.get_next_value(u_0);
    if (!success)
        return;


    motor_control(u_0);

    delay(2000);

    analogWrite(PWM_0, 255);
    analogWrite(PWM_1, 255);
    analogWrite(PWM_3, 255);
    analogWrite(PWM_5, 255);

    delay(2000);

    analogWrite(PWM_0, 0);
    analogWrite(PWM_1, 0);
    analogWrite(PWM_3, 0);
    analogWrite(PWM_5, 0);

    break;
}
```

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/hNo8b9V7_ss" title="Fast Robots Lab 8: Braking Flip Test" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

### Reversing
```c
case FLIP_R:  {
    
    float u_0;

    // Extract the next value from the command string as an integer
    success = robot_cmd.get_next_value(u_0);
    if (!success)
        return;


    motor_control(u_0);

    delay(1000);

    analogWrite(PWM_0, 255);
    analogWrite(PWM_1, 255);
    analogWrite(PWM_3, 255);
    analogWrite(PWM_5, 255);

    delay(500);

    motor_control(-u_0);

    delay(1000);

    analogWrite(PWM_0, 255);
    analogWrite(PWM_1, 255);
    analogWrite(PWM_3, 255);
    analogWrite(PWM_5, 255);


    delay(3000);

    analogWrite(PWM_0, 0);
    analogWrite(PWM_1, 0);
    analogWrite(PWM_3, 0);
    analogWrite(PWM_5, 0);

    break;
```

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/ltD8J51-5GQ" title="Fast Robots Lab 8: Reversing Flip Test" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

As shown in the videos, both flip methods initially failed despite using the full power of my motor, leaving me unsure of the issue. With help from a course staff member, I discovered that my battery was undercharged, reading only 3.7 to 3.8 volts instead of the expected 4.1 to 4.2 volts, which led to insufficient speed. After switching to a fully charged battery, a new issue appeared: when sending the signal for forward to both sets of wheels, only one side would spin. When sending the signal for backward, only the opposite side would spin instead. Faced with these motor control problems, I ultimately borrowed a classmate’s RC car to complete the stunt. With the new RC car, the only changes I needed to make for my code to work were updating the MAC address for the Artemis board, adjusting the motor control pin assignments, and modifying the calibration factor the motors.


## Trials

### Trial 1

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/rya5fWCRUj8" title="Fast Robots Lab 8: Trial 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>


### Trial 2

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/cNnAVAmn-Ns" title="Fast Robots Lab 8: Trial 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>


### Trial 3

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/l0L9wHbs8p4" title="Fast Robots Lab 8: Trial 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>


### Blooper

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/pkce_QrCZn8" title="Fast Robots Lab 8: Blooper" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

## Discussion

This lab taught me the importance of precise wiring when working with microcontrollers. The lab provided valuable hands-on experience in motor control and system calibration, laying the groundwork for future closed-loop control enhancements.

* * *

# Acknowledgements
*   I referenced Stephan Wagner's page.

