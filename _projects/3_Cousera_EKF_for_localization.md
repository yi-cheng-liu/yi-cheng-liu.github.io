---
title: "Error-State Extended Kalman Filter for Vehicle Localization"
excerpt: "use EKF to estimate the location <br/><img src='/images/projects/Cousera/Self_driving/estimation_trajectory.png'>"
collection: projects
---
## Goal

localize the vehicle with given dataset

## What was achieved

* Localized a vehicle with ES-EKF using prerecorded sensor data, including IMU, GPS, and LIDAR position updates
* Examined the effects of sensor miscalibration on vehicle pose estimates, and adjusted the filter to compensate for errors
* Investigated the effects of sensor dropout on the vehicle position estimate and the uncertainty in the position estimate

:computer: [Github repo](https://github.com/yi-cheng-liu/University-of-Toronto---self-driving-specialization/tree/main/Course%202%20-%20State%20Estimation)

## Final results

| Ground Truth             |  Estimation                    |
:-------------------------:|:-------------------------:
![ground truth trajectory](/images/projects/Cousera/Self_driving/ground_truth_trajectory.png)     |  ![estimation trajectory](/images/projects/Cousera/Self_driving/estimation_trajectory.png)