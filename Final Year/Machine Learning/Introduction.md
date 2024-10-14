# What is Machine Learning?
- The use of statistical analysis of existing data to infer a relation between I/O, with the goal of computing this relationship for new data
- Depending on the output data we distinguish
	- Classification
	- Regression
	- Clustering
- Depending on the input data we distinguish
	- Supervised learning algorithms
	- Unsupervised learning algorithms
	- Reinforcement learning algorithms

## What is Classification?
- Deciding what category an object belongs to based on a set of measured features
 ![[Pasted image 20241010151913.png]]
## What is Regression?
- Predicting a numerical quantity or relation between quantities based on a set of measurements
![[Pasted image 20241010151941.png]]

## What is Clustering?
- Determining which measurements in a dataset form a cluster
- i.e., considering similarity
![[Pasted image 20241010152017.png]]

## What is Supervised Learning?
- Using labelled training data to determine a functional relationship between input and output
- Generating / obtaining this training data is not easy as somebody needs to assign labels to large data sets
- It is very often crowd-sourced
## What is Unsupervised Learning?
- Discovering patterns in the data without labels based solely on similarity between data-points
- Examples: Dimensionality reduction, clustering
## What is Reinforcement Learning?
- Learning a strategy based on observed rewards
- requiring experimentation with the environment
- well-suited to synthetic environments such as video games, that can be repeated without cost or consequence

# A High-Level Overview of ML

- A typical ML problem can be divided into two phases: 
	- Learning, creating the model
		- Can be once off or continuous
	- Operational, applying the model to new data, generating results
		- Applying the model is much more efficient than learning

## The curse of Dimensionality
- Each additional feature adds another dimension to input space
- High dimensional spaces are empty, so the data represents less and less of the total volume
- Euclidean distances in high dimensional spaces are problematic, almost all distances are making equal objects indistingushable from each other
- n Points span an n-1 dimensional subspace (2 points -> 1d line, 3 points -> 2d plane, 4 points -> 3d volume, etc.), so any additional dimension is redundant
![[Pasted image 20241010152702.png]]

# Complexity and Overfitting
- The choice of the right complexity is crucial 
- When trying to predict a new data point, which line is better?
- Green fits the training set perfectly, yet it seems worse than the red one.
![[Pasted image 20241010152929.png]]
- Simpler models can generalise better
- What happens when a problem is more complex than the model allows?
- We either accept a small clasification error, or increase the complexity as required. 
![[Pasted image 20241010153147.png]]
- The evaluation step in the learning phase is where these decisions can be analyzed and justified. 