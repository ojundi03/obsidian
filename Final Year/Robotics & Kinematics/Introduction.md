# Some Basic Terms
- #**Links** - Rigid bodies that give structure to the robot.
- #**Joints** - The movable parts (actuators) that connect the links of the robot. Joints cause relative motion between adjacent links.
- **End Effector** - The last link of the robot arm or manipulator that interacts with the environment, e.g. gripper, welding gun, magnet.
- **Kinematic Chain** - The assembly of links connected by joints, to produce a desired motion (e.g. human arm).
![[Pasted image 20240915105425.png]]
## Degrees of Freedom
- Degrees of Freedom ( #DOF ) refers to the number of independent parameters that define a system's configuration.
- Three degrees of freedom are required to position an object in 3D space (translation).
- Another three degrees are required to arbitrarily set the orientation of the end effector (rotation).


![[Pasted image 20240915105608.png]]

## More Terms
- **Revolute Joint** - rotary motion *about* an axis.
- **Prismatic Joint** - linear, or sliding motion *along* an axis.
- **Arm, Major Axes** - Grossly positions the robot tool (end effector) - usually 3 #DOF.
- **Wrist, Minor Axes** - Finely positions end effector - requires up to 3 #DOF to provide correct orientation

## Servo Control #Servo
- **Non-Servo Controlled** - The joint is moved until the link hits an end-stop. Open loop control without sensory feedback.
- **Servo Controlled** - Position and/or velocity of robot links are accurately controlled using a feedback control system.
- **Point-to-point Motion** - A servo controlled system in which only the initial and final points of the motion are important and need to be accurately specified.
- **Continuous-Path Motion** - The entire motion is accurately controlled, e.g. collision avoidance.
- **Workspace** - The locus of points in 3D space that can be reached by the end effector. 

## More Terms
- **Actuators** #actuators
	- Electric: DC Motors, brushless motors, stepper motors, etc.
	- Hydraulic: Based on pistons compressing fluid.
	- Pneumatic: Air- compressible.
- **Workcell** - A collection of robot(s), fixtures, feeding devices, and other machines which are combined to perform one or more operations on some object(s).

# Measures of Performance
- Working volume needs to be sufficient so that all parts of the working area can be reached.
- **Speed** and **Acceleration** must be large enough so that the task can be accomplished within an acceptable time. 
- **Repeatability** - A measure of the tolerance within which the end effector can be returned to a pre-recorded point. 
- **Accuracy** - how close the end effector can get to a desired point.
	- Factors effecting Accuracy:
		- Frictional effects.
		- Discrete data effects (in sensors and computers)
		- Calibration errors.
	- Accuracy can be approved by additional sensory feedback.
- **Resolution** - The smallest step move that can be made at a given position.

# Robot Types
![[Pasted image 20240916085502.png]]

##  Cartesian Robots #cartesian
  ![[Pasted image 20240916085559.png]]
  - Cantilevered: #cantilevered
	  - Good accuracy and repeatabity.
	  - Easily programmed.
	  - Limited extension.
	  - Poor rigidity.
	  - Often limited in motion.
  - Gantry: #gantry
	  - Heavy loads.
	  - Rigid.
	  - Poor access to workspace.

## More Types
![[Pasted image 20240916085756.png]]
![[Pasted image 20240916085802.png]]

### Scara Robots #scara
![[Pasted image 20240916085821.png]]
- Selective compliance assembly robot arm
- Three axes are vertical
- Fast
- Smooth
- Useful for inserting objects in holes

### Articulated Robots #articulated
![[Pasted image 20240916085942.png]]
- E.g. - #PUMA (Programmable Universal Machine for assembly)
- Wide reach (near base and over obstacles)
- Anthropomorphic - waist, shoulder, elbow.