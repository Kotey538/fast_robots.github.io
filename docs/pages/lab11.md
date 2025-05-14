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




## Discussion

This lab emphasized the importance of accurate sensor data collection and reliable control when performing mapping tasks. It provided experience with IMU and ToF sensor data for 2D spatial understanding. The process of debugging the motor drivers, and requisition of data from the ToF and IMU reinforced the value of methodical testing and system-level troubleshooting. This lab laid a strong foundation for future work in localization and autonomous navigation.

* * *

# Acknowledgements
*   I referenced Stephan Wagner's page.

