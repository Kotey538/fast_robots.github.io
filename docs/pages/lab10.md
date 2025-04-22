---
title: "MAE 4910 Fast Robots"
description: "Lab 10: Grid Localization using Bayes Filter"
layout: default
---

# Lab 10: Grid Localization using Bayes Filter

In Lab 10, I implemented grid localization using a Bayes filter to estimate the robot's position within a 3D grid. By applying odometry data in the prediction step and incorporating sensor measurements in the update step, I updated the robot’s belief at each step. The grid with the highest probability indicated the most likely position, and I visualized the results to track the robot's trajectory and compare it to the ground truth pose.

* * *
 

## Helper Functions 

### Compute Control  
The `compute_control()` function calculates the odometry motion parameters between two poses. It determines the initial rotation using NumPy's `arctan2()` and normalizes it within [-180°, 180°]. The translation  is computed using `np.linalg.norm()` to efficiently measure the Euclidean distance. The final rotation is similarly calculated and normalized.

```python
def compute_control(cur_pose, prev_pose):
    """ Given the current and previous odometry poses, this function extracts
    the control information based on the odometry motion model.

    Args:
        cur_pose  ([Pose]): Current Pose
        prev_pose ([Pose]): Previous Pose 

    Returns:
        [delta_rot_1]: Rotation 1  (degrees)
        [delta_trans]: Translation (meters)
        [delta_rot_2]: Rotation 2  (degrees)
    """

    cur_pose = np.array(cur_pose)
    prev_pose = np.array(prev_pose)

    delta_pose = cur_pose - prev_pose

    # Calculate the first rotation, then normalize to (-180, 180)
    delta_rot_1 = mapper.normalize_angle(np.degrees(np.arctan2(delta_pose[1], delta_pose[0]) - prev_pose[2]))

    # Calculate the translation 
    delta_trans = np.linalg.norm(delta_pose[:2], axis=0)
    
     # Calculate the second rotation, then normalize to (-180, 180)
    delta_rot_2 = mapper.normalize_angle(delta_pose[2] - delta_rot_1)
    

    return delta_rot_1, delta_trans, delta_rot_2


```


### Odometry Motion Model

The `odom_motion_model()` function computes the likelihood of the robot transitioning from a previous pose to a current pose given control inputs. It leverages compute_control() to extract motion parameters, then calculates the probabilities for each motion component (rotations and translation) using Gaussian distributions centered on the provided control inputs. These individual probabilities are multiplied, assuming independence, to yield the overall transition probability.

```
def odom_motion_model(cur_pose, prev_pose, u):
    """ Odometry Motion Model

    Args:
        cur_pose  ([Pose]): Current Pose
        prev_pose ([Pose]): Previous Pose
        (rot1, trans, rot2) (float, float, float): A tuple with control data in the format 
                                                   format (rot1, trans, rot2) with units (degrees, meters, degrees)


    Returns:
        prob [float]: Probability p(x'|x, u)
    """
    deltas = np.array(compute_control(cur_pose, prev_pose))
    sigmas = [loc.odom_rot_sigma, loc.odom_trans_sigma, loc.odom_rot_sigma]

    prob = np.prod(loc.gaussian(deltas, u, sigmas))

    return prob
```

### Prediction Step 

The `prediction_step()` function implements the prediction stage of the Bayes filter. It iterates over a discretized 3D grid, computing transition probabilities using `odom_motion_model()` for all possible transitions. To enhance computational efficiency, grid cells with negligible belief values (below 0.0001) are skipped. The updated probabilities across the grid are then normalized to form a valid probability distribution.

```
def prediction_step(cur_odom, prev_odom):
    """ Prediction step of the Bayes Filter.
    Update the probabilities in loc.bel_bar based on loc.bel from the previous time step and the odometry motion model.

    Args:
        cur_odom  ([Pose]): Current Pose
        prev_odom ([Pose]): Previous Pose
    """
    u = compute_control(cur_odom, prev_odom)

    loc.bel_bar = np.zeros((mapper.MAX_CELLS_X, mapper.MAX_CELLS_Y, mapper.MAX_CELLS_A))

    for prev_x in range(mapper.MAX_CELLS_X):
        for prev_y in range(mapper.MAX_CELLS_Y):
            for prev_theta in range(mapper.MAX_CELLS_A):
                if (loc.bel[prev_x, prev_y, prev_theta] < 0.0001): continue

                for cur_x in range(mapper.MAX_CELLS_X):
                    for cur_y in range(mapper.MAX_CELLS_Y):
                        for cur_theta in range(mapper.MAX_CELLS_A):
                            p = odom_motion_model(mapper.from_map(cur_x, cur_y, cur_theta), mapper.from_map(prev_x, prev_y, prev_theta),u)

                            # Keep running sum of bel_bar
                            loc.bel_bar[cur_x, cur_y, cur_theta] += p * loc.bel[prev_x, prev_y, prev_theta]

    # Normalize bel bar
    loc.bel_bar /= np.sum(loc.bel_bar)
``` 


### Sensor Model 

The `sensor_model()` function calculates the likelihood of sensor measurements given a specific robot pose. Each sensor measurement’s probability is modeled independently as a Gaussian distribution, returning an array of likelihoods corresponding to each measurement.

```
def sensor_model(obs):
    """ This is the equivalent of p(z|x).


    Args:
        obs ([ndarray]): A 1D array consisting of the true observations for a specific robot pose in the map 

    Returns:
        [ndarray]: Returns a 1D array of size 18 (=loc.OBS_PER_CELL) with the likelihoods of each individual sensor measurement
    """
    prob_array = loc.gaussian(obs, loc.obs_range_data.flatten(), loc.sensor_sigma)
    return prob_array

```

### Update Step 

The `update_step()` function integrates sensor data into the predicted belief. For each grid cell, it uses `sensor_model()` to calculate measurement likelihoods, multiplies these with the predicted beliefs from the previous step, and normalizes the result. This produces an updated probabilistic estimate of the robot’s position within the discretized environment.



```
def update_step():
    """ Update step of the Bayes Filter.
    Update the probabilities in loc.bel based on loc.bel_bar and the sensor model.
    """
    for cur_x in range(mapper.MAX_CELLS_X):
        for cur_y in range(mapper.MAX_CELLS_Y):
            for cur_theta in range(mapper.MAX_CELLS_A):
                # Get the sensor model and update the belief
                p = sensor_model(mapper.get_views(cur_x, cur_y, cur_theta))
                loc.bel[cur_x, cur_y, cur_theta] = np.prod(p) * loc.bel_bar[cur_x, cur_y, cur_theta]

    # Normalize bel
    loc.bel /= np.sum(loc.bel)
```
## Simulation

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/fk9K2IcOY-U" title="Fast Robots Lab 10: Simulated Trajectory" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>

<div style="display: flex; justify-content: center; align-items: center; height: 100%;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/SfMDA2--pIk" title="Fast Robots Lab 10: Simulated Localization" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>
<br>


## Discussion

This lab highlighted the effectiveness of probabilistic localization using the Bayes filter. It emphasized accurately modeling sensor and motion uncertainties to reliably estimate the robot's pose. Optimizing computational efficiency in the prediction and update steps reinforced the importance of structured code and systematic debugging. Overall, this experience strengthened my understanding of probabilistic reasoning, laying the groundwork for robust localization and autonomous robot navigation tasks in future projects.

* * *

# Acknowledgements
*   I referenced Stephan Wagner's page.

