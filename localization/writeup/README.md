# Project 2: Localization


### 1. In the motion model plot, why are there more particles within a 10cm radius of the noise-free model prediction in Figure 2 than Figure 3?

For the noise free model, there is no uncertainty in the motion or the measurements. This results in a tight cluster of particles. In the absence of noise, this behaves deterministically.

In the noise induced model, there are randomized perturbations (which follow a Gaussian distribution) that cause the particles to spread out over time by following a normalized distribution. Inducing noise in our motion model gives a better realistic picture by taking sensor measurement and other real-world uncertainties into account. Hence, having a tighter cluster of particles in Figure 2 (100/100) does not exactly mean that the performance of the motion model is better.

Another reason why there is a tighter cluster is because in Figure 3 we can see that velocity and steering angles are higher. Hence, that also contributes to a divergence in the model predictions when compared to Figure 2.

### 2. Include mm1.png, mm2.png, and mm3.png, your three intermediate motion model plots for control (v, δ, dt) = (3.0,0.4,0.5). Use them to explain your motion model parameter tuning process. For each plot, explain how it differs from Figure 3, and how you changed the parameters in response to those differences. The last plot should reflect your final tuned parameters.

The key parameters of the motion model are:
- vel_std affects longitudinal motion uncertainty
- delta_std affects steering uncertainty
- x_std, y_std affect position uncertainty
- theta_std affects heading uncertainty

### Starting state

![Initial configurations](<Motion model 1.png>)

### MM1.png - Establish positional constraints

![MM1.png](<Motion model 2.png>)

- This figure shows a widely scattered distribution. This means that the particles are not following a coherent path.
- The image for the combination (v, delta, dt) = (1.0, 0.34, 0.1) also showed that only 80/100 particles were within 10cm of the mean.
- We need to constrain the particles to a more realistic motion pattern. We can first change that in the X-axis. Hence, we reduced x_std and y_std to 0.02.
- By adjusting the position uncertainty, the case (v, delta, dt) = (1.0, 0.34, 0.1) showed that 100/100 particles were within 10cm of the mean.
- For (v, delta, dt) = (3.0, 0.4, 0.5), only 3/100 particles were within 10cm of the mean after this change. This is because the longitudinal disposition is the main factor for this scattered distribution.

### MM2.png - Refine steering behaviour

![MM2.png](<Motion model 4.png>)

- We can adjust this lateral disposition by reducing the standard deviation of the steering angle - delta_std. We changed the value to 0.125.
- We can now see that more particles are coming within 10cm of the mean for (v, delta, dt) = (3.0, 0.4, 0.5) - smoother and more consistent trajectories and not very erratic steering angles.
- For (v, delta, dt) = (1.0, 0.34, 0.1), we observed that 100/100 particles were still within 10cm of the mean, and changes to the steering angle did not affect this distribution.
- However, to match it further, we need to reduce delta_std even more.

### MM3.png - Fine-tune steering behaviour

![MM3.png](<Motion model 5.png>)

- Further reducing delta_std to 0.05 ensured that we obtained a tight and coherent group of particles for (v, delta, dt) = (3.0, 0.4, 0.5)
- The final result closely matches the reference image, with a well-defined curved trajectory

Here is the motion model for the optimized paramters for (v, delta, dt) = (1.0, 0.34, 0.1)

![Optimized for (v, delta, dt) = (1.0, 0.34, 0.1).png](<Motion model - Initial plot optimized for same parameters.png>)


### 3. What are the drawbacks of the sensor model’s conditional independence assumption? How does this implementation mitigate those drawbacks? (Hint: we discussed this in class, but you can look at LaserScanSensorModelROS for the details.)

### Drawbacks

- The first drawback is **ignoring measurement correlations**. In reality, sensor measurements are often correlated. 
    - For instance, adjacent beam measurements from a LIDAR overlap or cover the same regions in the environment. 
    - This might lead to correlation of the measurements because the observation from one beam is now implicated by neighbouring beams.
    - However, under the independence assumption, the model ignores these correlations and treats each measurement as an independent sample - potentially leading to less accurate probability estimates.
    - 

- The second drawback is **misrepresenting sensor uncertainties**.
    - Since the model treats each reading to be independent, it might give too much confidence to a series of consistent measurements, even if they stem from some noise.
    - Assuming this conditional independence can lead to underestimation / overconfidence. The model does not account for shared information from different measurements.
    - It can make the measurements overconfident in its estimates.

- The third drawback is that the sensors might have **reduced robustness to noise and outliers**.
    - Sometimes, sensor noise might be correlated to different measurements, and needn't necessarily be independent in nature.
    - The conditional independence fails to mitigate errors / outliers from multiple beams if there are correlations between these different measurements.


**How we are mitigating these drawbacks**

- Sensor Noise Modeling
    - The sensor model includes noise parameters such as beam range noise, detection noise, and sensor accuracy limits. 
    - The Gaussian noise model reflects real-world uncertainties, reducing the chance of overconfidence in measurements.
    - By tuning these parameters appropriately, the model can better approximate the behavior of the real sensor, compensating for the independence assumption. 

- Downsampling Laser Rays
    - Instead of using all sensor readings (which increases the likelihood of correlated measurements affecting the estimate), we randomly sample a subset of laser beams or downsample the input. 
    - This reduces the likelihood of processing highly correlated data.

- Excluding Max Range Rays
    - Rays that reach the maximum sensor range often indicate open spaces or lack of obstacles, which can be less informative.
    - Excluding these rays helps in reducing the influence of noisy or less reliable measurements.

- Dynamic Weight Normalization
    - After computing the likelihoods of the sensor readings, the weights of the particles are normalized. 
    - This ensures that even if the independence assumption leads to some inflated likelihoods for certain particles, the normalization process prevents any single particle from becoming too dominant. 
    - It also maintains the diversity of particles, which is crucial for robust localization.

**4. Include sm1.png, sm2.png, and sm3.png, your three intermediate conditional probability plots for P(zkt|zk∗t ). Document the sensor model parameters for each plot and explain the visual differences between them.**

![Initial Map 1 configurations](<Sensor model 1 - map 1.png>)
![Initial Map 2 configurations](<Sensor model 1 - map 2.png>)

### SM1.png - Increase the weight of z_hit

![SM1-1.png](<Sensor model 2 - map 1.png>)
![SM1-2.png](<Sensor model 2 - map 2.png>)

- z_hit should be the highest because it represents the likelihood of the sensor returning the true distance to an obstacle.
- This is because, in a well-functioning sensor system, most measurements should be the actual reflections from real obstacles.
- We increase z_hit to 0.8 due to this.

### SM2.png - Reduce z_rand

![SM2-1.png](<Sensor model 3 - map 1.png>)
![SM2-2.png](<Sensor model 3 - map 2.png>)

- The starting value of z_rand seems very high, indicating that 50% of the sensor values are random.
- Reducing z_rand increases confidence in actual measurements versus random noise
- We reduced z_rand to 0.15 due to this.

### SM3.png - Increase 

![SM3-1.png](<Sensor model 4 - map 1.png>)
![SM3-2.png](<Sensor model 4 - map 2.png>)

- z_max represents the likelihood of getting maximum range readings, and these often occur if the sensors fail to hit any obstacle.
- Max range readings from sensors like LIDAR should be considered less informative during localization.
- Reducing this weight will ensure that the sensor model provides least importance to less informative readings.
- We reduced z_max to 0.02 and accordingly increased the z_short to 0.03

**Final conditional probability plot**

![SM-cond-prob-plot.png](<Sensor model - Final conditional probability.png>)

**5. Include your tuned sensor model likelihood plot for the robot positioned at state (−9.6,0.0,−2.5) in the maze_0 map**

Here is the output for the maze map using the tuned sensor model:

![Maze map output.png](<Sensor model - final map output.png>)

**6. Include your particle filter path plot for your 60-second simulated drive through CSE2.**
![Simulated drive - 60 seconds in CSE 2](<simulated_drive_60seconds_teleop.png>)

**Outputs from the bag tests**


**Circle**

![Circle path plot](<Circle_bag.png>)
![Circle errors](<Circle_bag_errors.png>)


**Full**

![Full path plot](<Full_bag.png>)
![Full errors](<Full_bag_errors.png>)


