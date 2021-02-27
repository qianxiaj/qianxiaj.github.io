---
title: "Target Following under Slow and Delayed Visual Feedback"
layout: post
date: 2019-9-1 22:10
tag: jekyll
image: 
headerImage: false
projects: true
hidden: true # don't count this post in blog pagination
description: "This is a simple and minimalist template for Jekyll for those who likes to eat noodles."
category: project
author: hui
externalLink: false
---
# Goal

Control the motion of a robot to follow and track a moving target based on only visual feedback.

# Challenges

- **Slow visual feedback**: Sampling speed of the vision system is slow compared to the sampling speed of the robot controller

- **Delayed visual feedback**: Measurements from the vision system contains significant time delay.

![Challenges](../assets/Projects Pictures/Target_following/Challange.png)

# Algorithm Overview

![algorithm](../assets/Projects Pictures/Target_following/algorithm_overview.png)

The block diagram consist of two feedback loop. The **inner loop** runs at a faster sampling speed, which controls each joint speed given the desired tool velocity. The **outer loop** is the visual servoing loop, which computes the desired tool velocity given the **slow** and **delayed** image feedback.

## Pose estimation

The goal is to estimate the 3D pose of the target from the images. If the target dimention is not given, we need to use a 3D vision system (i.e., RGB-D camera, or stereo cameras); if the target dimention is known as a prior, we can use a single calibrated camera to estimate the 3D pose (by solving the Perspective-N-point (PnP) problem). In this project, we used the second approach.

![pose_estimation](../assets/Projects Pictures/Target_following/pose_estimation.gif)

## Kalman filters

Kalman filters are used for measurement **noise reduction** and target **velocity estimation**. If the target is moving with high nonlinear trajectories, we can choose to use Extened Kalman filter or particle filter.

## Multirate Model-based Prediction (MMP)

A modified version of MMP (see my 2016 [paper](https://doi.org/10.1016/j.sysconle.2016.06.011)) was used to compensate the delays of the target velocity estimation and reconstruct a fast-sampled target position and velocity data.

![MMP](../assets/Projects Pictures/Target_following/MMP.gif)

# Experiment

We did a test on the dual-arm robot. The target was fixed on the left arm and moving with a sinusoid trajectory. The right arm was trying to track and follow the target movement provided only the images from the eye-in-hand camera.

![head_picture](../assets/Projects Pictures/Target_following/HardwareSetup.png)

## Without KFs and MMPs

We first tried the traditional position-based visual servoing (PBVS) approach. The tracking error was large as is shown below.

![level_1_robot](../assets/Projects Pictures/Target_following/leve1_robot.gif)
![level_1_camera](../assets/Projects Pictures/Target_following/leve1_camera.gif)

## With KFs but no MMPs

With the target velocity information estimated by the Kalmam filters, we can do a velocity compensation to reduce the tracking error. However, we still saw a significant error due to the slow sampling of camearas and the dealy.

![level_2_robot](../assets/Projects Pictures/Target_following/leve2_robot.gif)
![level_2_camera](../assets/Projects Pictures/Target_following/leve2_camera.gif)

## With KFs and MMPs

If we added MMPs to compensate the delay and recovered a fast-sampled trajectory speed profile, we saw an optimal tracking performance.

![level_4_robot](../assets/Projects Pictures/Target_following/leve4_robot.gif)
![level_4_camera](../assets/Projects Pictures/Target_following/leve4_camera.gif)

# Reference
[1] **Hui Xiao**, Xu Chen. “Following Fast-Dynamic Targets with Only Slow and Delayed Visual Feedback— A Kalman Filter and Model-Based Prediction Approach.” **In Proceedings of ASME 2019 Dynamic System and Control Conference**, Oct. 9, Park City, Utah, 2019. (**Best Student Paper on Robotics**)
