---
title: "MAE 4910 Fast Robots"
description: "Lab 11: Localization on the real robot"
layout: default
---

# Lab 11: Localization on the Real Robot

In Lab 11, I applied the Bayes filter on the real robot to perform localization using only the update step. Due to noisy motion, the prediction step was omitted, and localization was achieved by collecting a full 360-degree scan of distance measurements at 20-degree increments. The robot’s belief distribution was updated using these sensor readings, and the most likely position was identified as the grid cell with the highest probability. I repeated this process at four known ground truth positions to evaluate accuracy. The results were visualized and compared to the expected poses, highlighting differences in performance between simulation and real-world conditions.

* * *
 
## Test Localization in Simulation

I began the lab by running the notebook lab11_sim.ipynb to verify that the provided code correctly implemented the Bayes filter on the virtual robot. This served as a baseline to ensure that the localization module functioned as expected and mirrored the functionality developed in Lab 10.

![image](../images/lab11/sim.PNG)

From the results of the simulated localization, it is clear that the odometry model (red) deviates significantly from the actual trajectory. In contrast, the belief estimate (blue) closely aligns with the ground truth data (green), demonstrating the effectiveness of the Bayes filter’s update step in correcting for odometry errors.

## Implementation for  Real Robot

To implement localization on the real robot, I completed the `perform_observation_loop()` function in `lab11_real.ipynb`. I reused the mapping routine developed in Lab 9, as it was already configured to collect Time-of-Flight sensor readings at 20° increments, which aligned well with the requirements of this lab.

```python
def perform_observation_loop(self, rot_vel=120):
        """Perform the observation loop behavior on the real robot, where the robot does  
        a 360 degree turn in place while collecting equidistant (in the angular space) sensor
        readings, with the first sensor reading taken at the robot's current heading. 
        The number of sensor readings depends on "observations_count"(=18) defined in world.yaml.
        
        Keyword arguments:
            rot_vel -- (Optional) Angular Velocity for loop (degrees/second)
                        Do not remove this parameter from the function definition, even if you don't use it.
        Returns:
            sensor_ranges   -- A column numpy array of the range values (meters)
            sensor_bearings -- A column numpy array of the bearings at which the sensor readings were taken (degrees)
                               The bearing values are not used in the Localization module, so you may return a empty numpy array
        """


        ble.start_notify(ble.uuid['RX_STRING'], notification_handler)
        ble.send_command(CMD.MAPPING, "0.1|1.5|3000")

        while (len(dist_1)<18):
            asyncio.run(asyncio.sleep(1))
            
        ble.stop_notify(ble.uuid['RX_STRING'])
            
        sensor_ranges = np.array(distance_data)[np.newaxis].T
        sensor_bearings = np.array(yaw_data)[np.newaxis].T


        return sensor_ranges, sensor_bearings
```

## Test Localization in Reality


### (5,-3)

<div style="display: flex; justify-content: center; align-items: center; gap: 20px; flex-wrap: wrap;">
  <img src="../images/lab9/(5,-3).png" alt="(5,-3)" style="max-width: 100%; height: auto; width: 300px;" />
  <iframe width="560" height="315" src="https://www.youtube.com/embed/R6e5773sN3I" title="Fast Robots Lab 9: Mapping (5,-3)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>


### (5,3)

<div style="display: flex; justify-content: center; align-items: center; gap: 20px; flex-wrap: wrap;">
  <img src="../images/lab9/(5,3).png" alt="(5,3)" style="max-width: 100%; height: auto; width: 300px;" />
  <iframe width="560" height="315" src="https://www.youtube.com/embed/jvro13ow9_Y" title="Fast Robots Lab 9: Mapping (5,3)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

### (0,3)

<div style="display: flex; justify-content: center; align-items: center; gap: 20px; flex-wrap: wrap;">
  <img src="../images/lab9/(0,3).png" alt="(0,3)" style="max-width: 100%; height: auto; width: 300px;" />
  <iframe width="560" height="315" src="https://www.youtube.com/embed/51t4djG8ICU" title="Fast Robots Lab 9: Mapping (0,3)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

### (-3,-2)

<div style="display: flex; justify-content: center; align-items: center; gap: 20px; flex-wrap: wrap;">
  <img src="../images/lab9/(-3,-2).png" alt="(-3,-2)" style="max-width: 100%; height: auto; width: 300px;" />
  <iframe width="560" height="315" src="https://www.youtube.com/embed/2Hy-CPEAqes" title="Fast Robots Lab 9: Mapping (-3,-2)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

## Discussion

This lab emphasized the challenges of real-world localization compared to simulation. Working with the physical robot reinforced the importance of accurate sensor alignment and consistent rotation behavior. The experience deepened my understanding of probabilistic localization and demonstrated the gap between idealized models and practical implementation.

* * *

# Acknowledgements
*   I referenced Stephan Wagner's page.
*   I used Josh Simpson's RC car

