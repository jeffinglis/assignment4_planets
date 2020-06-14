# Assignment 4 Planets study data analysis

[![Binder](https://mybinder.org/badge_logo.svg)](https://notebooks.gesis.org/binder/v2/gh/jeffinglis/assignment4_planets/52b0f2b68a8b01eb6245d3e62de4410b1f3e61d1)

# Data Science Project 


I decided to take this opportunity to use what I learned in this course to redo some analysis I did for a project that has stagnated and I really need to finish.There are a couple things I hope to get from this.:

1. My original code was quite messy. I am hoping to clean it up using some of the tools from this course.
2. I want to be sure that I can replicate my results. 
3. I want to compare my models performance to NMF learned in this course.
4. I want to set up google cloud console, github, and binder so that my code can be made available for reproducibility

It should be noted that the functions "sensoryFunctionOptimizer(x0)" and "preferenceFunctionOptimizer(x0)" were part of a single function that was written and implemented prior to this project. However, these two functions needed to be adapted significantly for this project. All other work here is novel (including preparation of the data for analysis).

## Introduction to the model


This data comes from a collaboration with dynamical neuroscience (my department), computer science and physics departments. The physics department will be sending a probe into deep space to take photos of planets. The probe will have limited memory and will lose contact with earth for extended periods of time. Therefore, the probe will need to decide on its own what images to keep and send back to earth and what images to delete. The role of the computer scientists is to build a machine learning algorithm to make this decision. However, the ML algorithm needs to know what humans think would be important images to keep and which ones to delete. 

This led to us create a model (extend a similar already existing unidimensional model to multiple dimensions, see Ashby and Ennis) that assumes participants use sensory information to make liking judgements. 

The model assumes that each items location in sensory space is represented by a sensory multidimensional normal distribution (SMVN). Additionally, the model also assumes that there exists an 'ideal' multivariate normal distribution (IMVN) that represents the 'ideal' items location in sensory space. When considering a rating on the sensory dimension, the subjects perception is represented as a sample from the SMVN. Additionally, the subject lies down 6 criteria on the sensory dimension (creating 7 bins). Each bin is associated with a rating from 1-7 that increases with magnitude on the sensory dimension (i.e. with 1 representing the bin from the first criterion to negative infinity and 7 representing the bin from the sixth criterion to positive infinity). The subject determines the bin that it's sensory perception falls in and reports that rating. When a liking judgement is required, the subject imagines their ideal item (represented as a sample from the ideal MVN) and they compare the distance between the perceived item (sample) and the ideal sample. Once again the subject lays down 6 criteria but this time on a "distance" dimension (distance between perceived item and ideal). If the perceived item falls within the bin closest to the peak of the ideal distribution then the subject will rate it a 7. Alternatively, if the perceived item falls in the bin between the furthest criteria and infinity then the subject will rate it a 1.

Consider a simple one-dimensional example (shown in the figure below). Participants are presented with a number of cups of coffee, all varying on one dimenion - bitterness (sensory magnitude). When presented with a cup of coffee the participants are asked to provide two ratings from 1-7: a bitterness judgement and a liking judgement. The model assumes that the participants perception of bitterness is a sample from a univariate normal distribution. In the figure, x represents a specific sample located at -1.8 and this sample falls into the bin between criteria such and such and therefore the cup of coffee is rated a four (moderate) on bitterness. Furthermore, the model assumes the participant represents the ideal cup of coffee as a sample from unidimensional distribution on bitterness. In the figure, y represents a specific sample located at 1.5. The subject then computes the absolute difference between the two samples (percept and ideal). The distribution of these differences is shown in figure 1c which is folded univariate normal distribution. The criteria lie on this difference dimension and the participants reported liking rating reflects the bin that the computed difference falls within. 

In the following section I will fit the model to the multidimensional case. In this experiment, 27 participants reported 2 sensory and 2 liking ratings for each of 20 planets on 6 dimensions (Water, Clouds, Rings, Moons, Blue-green color, Red-yellow color). This resulted in 56 liking judgements for each dimension and liking (here, I treat each rating as if it came from a different subject, the effect of this will need to be established in future work). This results in two data matrices: a sensory matrix which is 20 (planets) * 6 (sensory dimensions) * 7 (rating) and a liking matrix which is 20 (planets) * 7 (ratings). Each cell in the matrix reports the frequency with which the rating was chosen on that dimension for that planet. In order to fit the model, I chose to first fit the sensory parameters to the sensory data matrix. Then I fixed the sensory parameters and fit the additional liking parameters to the liking data matrix.

The sensory parameters consist of:

- 114 means (19 planets * 6 dimensions, 1 planets means were set to 0)
- the covariance matrix is assumed to be the identity for each planet (0 free parameters)
- 36 criteria (6 criteria * 6 dimensions)

The liking parameters consist of:

- 6 ideal means (one for each dimension)
- 21 covariance matrix parameters (a 6* 6 covariance matrix)
- 6 ideal criteria

For a total of 183 parameters

