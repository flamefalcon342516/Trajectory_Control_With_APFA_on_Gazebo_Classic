# 🚀 Trajectory Control with Artificial Potential Field (APF) & Real-Time Visualization  
Gazebo Classic + ROS 2 + Python  

**Live demonstration of multi-goal autonomous navigation + obstacle avoidance + real-time plotting — both in simulation *and* on a real rover!**

---

## 📘 Project Description  

This project implements autonomous navigation for a differential-drive robot in **Gazebo Classic**, using the **Artificial Potential Field (APF)** algorithm for path control and obstacle avoidance. The controller takes data from odometry and a depth-camera point cloud, computes attractive and repulsive forces in real-time, and commands velocity via `/cmd_vel`.  

Simultaneously, it visualizes robot behaviour live — plotting the robot’s path and heading error dynamically — giving instant feedback during runs.  

> **Also:** The same codebase has been tested on a real rover (see linked repo), demonstrating applicability beyond simulation.

---

## 🧠 APF Theory & Implementation  

### Potential Fields  

We model the environment as a potential field:  
- The goal exerts an *attractive potential* pulling the robot toward it.  
- Obstacles create *repulsive potentials* pushing the robot away.  

The robot moves by following the **negative gradient** (i.e., steepest descent) of the total potential:

\[
U_{\text{total}}(q) = U_{\text{att}}(q) + \sum_{i=1}^{N} U_{\text{rep},i}(q)
\]  

Where:  
- \(q = (x, y)\) is the robot position  
- \(U_{\text{att}}\) is the attractive potential  
- \(U_{\text{rep},i}\) are repulsive potentials for each obstacle \(i\) :contentReference[oaicite:1]{index=1}

A common choice:  

\[
U_{\text{att}}(q) = \frac{1}{2} \, k_{\text{att}} \, \|q - q_{\text{goal}}\|^2
\]  

\[
U_{\text{rep},i}(q) = 
\begin{cases}
\frac{1}{2} \, k_{\text{rep}} \left(\frac{1}{d(q, q_{\text{obs},i})} - \frac{1}{d_0} \right)^2, & \text{if } d(q, q_{\text{obs},i}) \le d_0 \\
0, & \text{if } d(q, q_{\text{obs},i}) > d_0
\end{cases}
\]

Where:  
- \(d(q, q_{\text{obs},i})\) is distance to obstacle \(i\)  
- \(d_0\) is the influence distance beyond which obstacle has no effect  

The resulting **forces** driving the robot are:  

\[
\mathbf{F}_{\text{att}} = -\nabla U_{\text{att}}, \quad
\mathbf{F}_{\text{rep}} = -\sum_i \nabla U_{\text{rep},i}
\]  

Then,  

\[
\mathbf{F} = \mathbf{F}_{\text{att}} + \mathbf{F}_{\text{rep}}
\]

The robot moves in the direction of \(\mathbf{F}\); in a differential-drive robot, this is converted to linear and angular velocity commands. :contentReference[oaicite:2]{index=2}  

---

## 🎯 Our Implementation  

- **Waypoints / Multi-goal navigation** — The robot visits a sequence of goals in order.  
- **Live obstacle avoidance** — Using depth-camera point cloud, obstacles within a region of interest are considered in the repulsive field.  
- **Force computation & velocity command** — Attractive and repulsive forces are combined, normalized, and mapped to `[linear, angular]` velocity commands with clipping.  
- **Real-time visualization** — Using `matplotlib`, robot’s path and heading error are plotted live, updating every control cycle.  
- **Gazebo Classic compatibility** — Tested in Gazebo Classic environment.  
- **Real-world deployment ready** — The same logic has been tested on a physical rover (see [APFA_rover_implementation] repository).  

---

## 📦 Repo Structure  

