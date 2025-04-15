---
title: "MAE 4910 Fast Robots"
description: "Lab 9: Mapping"
layout: default
---

# Lab 9: Mapping

In Lab 9, I implemented a mapping routine to generate a spatial representation of a static room using my RC robot. By rotating the robot in place at marked locations, I collected Time-of-Flight (ToF) distance measurements and used orientation data from the IMU to associate each measurement with an angle. I then transformed these local readings into a global reference frame using transformation matrices. Finally, I visualized the mapped environment and approximated the walls using line segments to create a line-based map for future localization and navigation tasks.

* * *
 
## Prelab

I then focused on fixing the implementation of the stunt command from Lab 8 since Lab 9 required reliable collection of both ToF and IMU data, which I was unable to achieve during the stunt routine. Although the motors were controlled correctly, the function only collected sensor data for approximately 0.4 seconds even though the intended runtime was 5 seconds. By inserting Serial print statements to debug the function, I discovered that the index variable was being incremented outside the condition that checks for valid sensor data. This caused empty entries in the data arrays and led the program to exit the loop early when the condition `if (time_data[j] != 0)`  failed. To resolve this, I modified the logic so that the index increments only when valid sensor data is received.
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

Due to time constraints, I opted for open-loop control, which limited my ability to precisely control the RC car’s return. If I had implemented orientation control as in Lab 6, I would have had much better control during the return phase.

```c
case STUNT2:  {
    
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

    motor_control(-0.5*u_0);


    delay(2000);

    analogWrite(PWM_0, 0);
    analogWrite(PWM_1, 0);
    analogWrite(PWM_3, 0);
    analogWrite(PWM_5, 0);

    break;
}
```

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

This lab showed the limitations of only using open loop control, reinforcing the value of closed-loop systems like those implemented in earlier labs.

* * *

# Acknowledgements
*   I referenced Stephan Wagner's page.
*   I used Josh Simpson's RC car

