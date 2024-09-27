## CHEAT SHEET
- $\{L_A\}$ = Position
	- Formed by  $\hat{x}^A,\hat{y}^A,\hat{z}^A$ 
	- $p^A$ is a point on this #WRT (with respect to) system
- $\{L_B\}$ = Orientation
	-  Formed by  $\hat{x}^B,\hat{y}^B,\hat{z}^B$ 
- The orientation relative to position is described in the 3x3 Matrix $R^B_A$ found [here](obsidian://open?vault=Obsidian&file=Pasted%20image%2020240916093219.png)


- How many Variables do you need to describe the car in relation to the 2D frame shown?
![[Pasted image 20240916090632.png]]
- Anwser: **3** $\begin{equation}x,y, \theta\end{equation}$
- $\begin{equation}(x,y)\end{equation}$ describes position and $\begin{equation}\theta \end{equation}$ describes orientation.

## Position
![[Pasted image 20240916091833.png]]
- $p^A$ represents a point #coordinate system $\{L_A\}$ 
- $\hat{x}^A,\hat{y}^A,\hat{z}^A$ form an orthonormal right-handed set

![[Pasted image 20240916092258.png]]

## Orientation
- Frame $\{L_B\}$ is defined by unit vectors $\hat{x}^B,\hat{y}^B,\hat{z}^B$
![[Pasted image 20240916092445.png]]

## Combining Things
- The orientation of frame $\{L_B\}$ relative to frame  $\{L_A\}$ is described by the 3 x 3 rotation matrix
##### ${R^B_A} = [(\hat{x}^B)^A (\hat{y}^B)^A (\hat{z}^B)^A]$
- Where $(\hat{x}^B)^,(\hat{y}^B)^A, (\hat{z}^B)^A$ are the unit x,y, and z-axis  basis vectors of frame $\{L_B\}$, expressed relative to the frame $\{L_A\}$ axes, i.e:
 $\LARGE \begin{equation} (\hat{x}^B)^A = \begin{bmatrix} \hat{x}^B \cdot \hat{x}^A \\\hat{x}^B \cdot \hat{y}^B \\ \hat{x}^B \cdot \hat{z}^B  \end{bmatrix}\end{equation}$
- The rotation Matrix $R^B_A$ is the rotation that must be applied to $\{L_A\}$ to place it at $\{L_B\}$ .

