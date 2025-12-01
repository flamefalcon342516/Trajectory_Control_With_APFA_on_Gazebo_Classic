# 🚀 Trajectory Control with APF & Real-Time Visualization on Gazebo Classic  
ROS2 · Python · Gazebo Classic · Obstacle Avoidance · Real Rover Deployment

This repository demonstrates multi-goal autonomous navigation for a differential-drive robot using the **Artificial Potential Field (APF)** algorithm with **real-time visualization** of robot path and heading error. The system runs in **Gazebo Classic**, and has also been **tested on a real-world rover**, validating simulation-to-reality transfer.

---

## 🧠 Artificial Potential Field (APF) Theory

APF treats navigation as motion within a potential field, where:

- The **goal** attracts the robot
- **Obstacles** repel the robot
- The robot follows the **resultant force vector**

### **Total Potential Field**
\[
U_{\text{total}}(q) = U_{\text{att}}(q) + \sum_{i=1}^{N} U_{\text{rep},i}(q)
\]

### **Attractive Potential**
\[
U_{\text{att}}(q) = \frac{1}{2} k_{\text{att}} \| q - q_{\text{goal}} \|^2
\]

### **Repulsive Potential**
\[
U_{\text{rep},i}(q) =
\begin{cases}
\frac{1}{2} k_{\text{rep}} \left( \frac{1}{d(q,q_{\text{obs},i})} - \frac{1}{d_0} \right)^2, & \text{if } d(q,q_{\text{obs},i}) \le d_0 \\
0, & \text{if } d(q,q_{\text{obs},i}) > d_0
\end{cases}
\]

Where:  
- \( d(q,q_{\text{obs},i}) \) is distance to obstacle \(i\)  
- \( d_0 \) is the influence radius for obstacles  

### **Force Calculations**
\[
\mathbf{F}_{\text{att}} = -\nabla U_{\text{att}}
\]
\[
\mathbf{F}_{\text{rep}} = -\sum_{i=1}^{N} \nabla U_{\text{rep},i}
\]
\[
\mathbf{F} = \mathbf{F}_{\text{att}} + \mathbf{F}_{\text{rep}}
\]

The resultant force vector determines robot motion direction.  
In differential-drive robots this force is converted into linear and angular velocity.

---

## 🎯 Implementation Summary

### **What the controller does**
- Subscribes to `/odom` for pose and `/depth_camera/points` for obstacle perception
- Computes attractive & repulsive forces in every control cycle
- Selects next goal automatically when close enough to current target
- Converts force direction into real velocity commands
- Plots robot trajectory and heading error live during execution

### **Control Logic**
| Condition | Action |
|-----------|--------|
| No obstacles detected | Move toward goal using heading error |
| Obstacle detected | Adjust direction using APF repulsive bias vector |
| Goal reached | Switch to next waypoint |

---

## 📈 Real-Time Visualization
The controller produces live graphs using matplotlib:
- Robot trajectory vs time
- Heading error vs time (stability metric)

This helps evaluate controller performance visually.

---

## 🤖 Real Rover Deployment

This controller has been implemented on a **real Pixhawk-based rover**, using depth sensing for obstacle avoidance and MAVROS for command forwarding. The real-world implementation repository is here:

🔗 **https://github.com/flamefalcon342516/APFA_rover_implementation**

This demonstrates sim-to-real capability and the feasibility of APF-based navigation outside simulation.

---

## 🚀 How To Run

### Build Workspace
```bash
cd ~/ros2_ws/src
git clone https://github.com/flamefalcon342516/Trajectory_Control_With_APFA_on_Gazebo_Classic.git
cd ~/ros2_ws
colcon build --symlink-install
source install/setup.bash
