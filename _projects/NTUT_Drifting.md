---
title: "Drifting Stabilization Along Circular Trajectories"
excerpt: "visualize the drifting trajectory <br/><img src='/images/projects/NTUT/drifting_architecture.png'>"
collection: projects
---

## Goal

Drifting control is the ability to maintain control of a drifting vehicle while executing high-speed, controlled sideslips around corners. The purpose of building a drifting controller can help prevent accidents and allow the vehicle to maintain high speeds and make precise maneuvers.

## What we achieved

* Stabilized the side-slip angle to match different desired curvatures and realize drifting motion
* Achieved error dynamics and nonlinear model inversion to calculate desired operating points, steering angle, yaw rate, rear drive force and longitudinal velocity
* Designed LQR controller for steering angle and proportional control for rear drive force
* Held course angle error under 0.9 degrees and visualized the high side-slip maneuver process in Simulink

## Final Report

<iframe src="https://drive.google.com/file/d/1bVhZXYxDoGGf3J8VWpzKh8qUZeTXuqPU/preview" width="640" height="480" frameborder="0"></iframe>

## Final results

<iframe width="560" height="315" src="https://www.youtube.com/embed/wwEMqDIFKgo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
