# Mushur Autonomous Vehicle Project

This project implements a comprehensive self-driving car system divided into three primary modules: **Localization**, **Planning**, and **Control**. Each module is designed to tackle specific challenges in autonomous navigation, ensuring safe and efficient operation.

---

## **1. Localization Module**
The **Localization** module is responsible for determining the precise position and orientation of the vehicle within its environment. This is achieved through the integration of various algorithms and sensor data.

### Key Features:
- **Sensor Fusion**: Combines data from GPS, IMU, and LiDAR to improve accuracy and reliability.
- **SLAM (Simultaneous Localization and Mapping)**: Dynamically generates a map of the environment while estimating the vehicle’s position in real-time.
- **Particle Filter**: Ensures robust state estimation in dynamic environments.

---

## **2. Planning Module**
The **Planning** module generates a safe and efficient trajectory for the vehicle, considering both static and dynamic obstacles in its path.

### Key Features:
- **Global Path Planning**: Uses algorithms like A* or Dijkstra to calculate an optimal route from the vehicle’s current location to its destination.
- **Local Path Planning**: Implements methods like the Dynamic Window Approach (DWA) or RRT* to adapt the trajectory in real-time based on nearby obstacles.
- **Road Damage Avoidance**: (Work In Progress) Integrates road damage detection algorithms to treat potholes as obstacles, allowing the vehicle to safely navigate around them.
- **Behavior Planning**: Determines high-level actions (e.g., lane changes, stops) based on traffic rules and environmental context.

---

## **3. Control Module**
The **Control** module ensures the vehicle follows the planned trajectory smoothly and safely while maintaining stability.

### Key Features:
- **PID Controller**: Implements Proportional-Integral-Derivative control for smooth steering, throttle, and braking adjustments.
- **Model Predictive Control (MPC)**: Utilizes a dynamic vehicle model to predict future states and optimize control inputs.
- **Emergency Braking System**: Overrides normal operation in critical scenarios to prevent collisions.

---

## **System Overview**
1. The **Localization Module** provides precise positional data to the **Planning Module**.
2. The **Planning Module** generates an optimal path based on current position, destination, and environmental factors.
3. The **Control Module** executes the planned path by sending control signals to the vehicle’s actuators.


## **Future Work**
- Integration of advanced machine learning algorithms for more accurate perception and decision-making.
- Testing and deployment of road damage detection algorithms in real-world scenarios.
- Optimization of computational performance for real-time operations.