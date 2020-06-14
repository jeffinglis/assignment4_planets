# Assignment 4 Planets study data analysis

[![Binder](https://notebooks.gesis.org/binder/badge_logo.svg)](https://gesis.mybinder.org/binder/v2/gh/jeffinglis/assignment4_planets/f513a993bc6b355974993ab580e27fccc7245b70)

# Data Science Project 
***Jeff Inglis 5004536***


I decided to take this opportunity to use what I learned in this course and apply it to a project I was working on but had stagnated. In brief, in collaboration with my advisor, Greg Ashby, we have extended a psycholological model of how humans derived liking judgements from sensory perceptions. The original model was unidimensional and our extension enables the model to account for multiple sensory dimensions. The original model can be found here https://onlinelibrary-wiley-com.proxy.library.ucsb.edu:9443/doi/abs/10.1111/j.1745-459X.2002.tb00331.x. From here on, the novel multivariate model will be referred to as the "Multidimensional Thurstone-Coombs (MTC) model"

## Project outline

1. I will use some of the tools from this course to wrangle my data
2. I want to spend a significant amount of time creating plots to illustrate my findings. The images used as stimuli in this project are very beautiful and it would be nice to develop plots that show them off.
3. I want to apply NMF and PCA to my data and see if I can find relationships between these modeling approaches and and the parameters of the MTC. 


The project is also available in a github repository https://github.com/jeffinglis/assignment4_planets and on binder:

[![Binder](https://notebooks.gesis.org/binder/badge_logo.svg)](https://gesis.mybinder.org/binder/v2/gh/jeffinglis/assignment4_planets/f513a993bc6b355974993ab580e27fccc7245b70)



It should be noted that the functions "sensoryFunction(x0)" and "preferenceFunction(x0)" were part of a single function that was written and implemented prior to this project. However, these two functions needed to be adapted significantly for this project. All other work here is novel.

## Introduction to the model


This data comes from a collaboration with dynamical neuroscience (my department), computer science and physics departments. The physics department will be sending a probe into deep space to take photos of planets. The probe will have limited memory and will lose contact with earth for extended periods of time. Therefore, the probe will need to decide on its own what images to keep and send back to earth and what images to delete. The role of the computer scientists is to build a machine learning algorithm to make this decision. However, the ML algorithm needs to know what humans think would be important images to keep and which ones to delete. 

This led to us create a multidimensional extension of a unidimensional model that assumes participants use derive liking judgements from sensory perceptions. 

The model assumes that each items location in sensory space is represented by a sensory multidimensional normal distribution (SMVN). Additionally, the model also assumes that there exists an 'ideal' multivariate normal distribution (IMVN) that represents the 'ideal' items location in sensory space. When considering a rating (from 1-7) on the sensory dimension, the subjects perception is represented as a sample from the SMVN. Additionally, the subject lies down 6 criteria on the sensory dimension (creating 7 bins). Each bin is associated with a rating from 1-7 that increases with magnitude on the sensory dimension (i.e. with 1 representing the bin from the first criterion to negative infinity and 7 representing the bin from the sixth criterion to positive infinity). The subject determines the bin that it's sensory perception falls in and reports that rating. When a liking judgement is required, the subject imagines their ideal item (represented as a sample from the IMVN) and they compare the distance between the perceived item (sample) and the ideal sample. Once again the subject lays down 6 criteria but this time on a "distance" dimension (distance between perceived sensory sample and ideal sample). If the perceived item falls within the bin closest to the ideal sample then the subject will rate it a 7. Alternatively, if the perceived item falls in the bin between the furthest criteria and infinity then the subject will rate it a 1 (with intermediate ratings rsulting from falling in intermediate bins).

Consider a simple one-dimensional example. Participants are presented with a number of cups of coffee, all varying on one dimenion - bitterness (sensory magnitude). When presented with a cup of coffee the participants are asked to provide two ratings from 1-7: a bitterness judgement and a liking judgement. The model assumes that the participants perception of bitterness is a sample from a univariate normal distribution. Furthermore, the model assumes the participant represents the ideal cup of coffee as a sample from unidimensional distribution on bitterness. The subject then computes the absolute difference between the two samples (sensory percept and ideal). The criteria lie on this difference dimension and the participants reported liking rating reflects the bin that the computed difference falls within. Intuitively, the subject will prefer the coffee with bitterness most similar to their ideal.

In the following section I will fit the model to the multidimensional case. In this experiment, 29 participants reported 2 sensory and 2 liking ratings for each of 20 planets on 6 dimensions (Water, Clouds, Rings, Moons, Blue-green color, Red-yellow color). This resulted in 58 judgements for each sensory dimension and the liking dimensiom (here, I treat each rating as if it came from a different subject, the effect of this will need to be established in future work). 

The sensory parameters consist of:

- 114 means (19 planets * 6 dimensions, 1 planets means were set to 0)
- the covariance matrix is assumed to be the identity for each planet (0 free parameters)
- 36 criteria (6 criteria * 6 dimensions)

The liking parameters consist of:

- 6 ideal means (one for each dimension)
- 21 covariance matrix parameters (a 6* 6 covariance matrix)
- 6 ideal criteria

For a total of 183 parameters

