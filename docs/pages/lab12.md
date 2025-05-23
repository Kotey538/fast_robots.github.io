---
title: "MAE 4910 Fast Robots"
description: "Lab 12: Path Planning and Execution"
layout: default
---

# Lab 12: Path Planning and Execution

In Lab 12, I utilize skills gained throughout the course in order to enable autonomous waypoint navigation in a mapped environment. The robot successfully navigated from the starting point to several intermediate goals.

* * *
 
## Prelab

Considering I was using Josh's robot for doing this lab as mine was not operating I decided to cooperate with him in order to complete this lab.  We decided to do open loop control as it would be the easiest implementation.

## Implementation

```c
case NAVIGATE:
    drive_straight(true, v0);
    delay(d0);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(true, v1);
    delay(d1);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v2);
    delay(d2);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(true, v3);
    delay(d3);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v4);
    delay(d4);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(false, v5);
    delay(d5);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v6);
    delay(d6);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(false, v7);
    delay(d7);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v8);
    delay(d8);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(false, v9);
    delay(d9);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v10);
    delay(d10);
    drive_straight(true, 0);
    delay(1000);
    
    drive_rot(false, v11);
    delay(d11);
    drive_straight(true, 0);
    delay(1000);
    
    drive_straight(true, v12);
    delay(d12);
    drive_straight(true, 0);
    break;
```

## Results

<div style="text-align: center;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/Gu4HOQactlY" 
    frameborder="0" allowfullscreen></iframe>
</div>



* * *

# Acknowledgements
*   Collaborate with Josh Simpson

