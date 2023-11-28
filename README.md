# Turbidity_prediction_with_SMOF

Project description:

The list of noisy bands tabulated in this study can be used globally for any application on PRISMA data. Moreover, Machine learning based feature selection is another topic which is quite unexplored in water quality analysis,
which we have taken up in this research work. Feature selection is crucial in predicting water quality parameters using hyperspectral satellite data to reduce the computational expense, complexity and also to simplify the
learning process of machine learning algorithms. This study can be considered as a standard comparison among various machine learning algorithms to extract feature importance scores with large number of predictor variables.
We've also developed a novel framework for turbidity prediction namely Stacked Ensemble with Machine Learning Regressors on Optimal Features (SMOF). 
It employs a stacking ensemble of the nine above mentioned regressors with Random Forest as both base and meta model, leveraging feature selection outputs. 
The uniqueness of our approach emerges when the best-performing model from the feature selection procedure assumes a dual role – as a base model and a meta model. 
Moreover, in this unique approach, the top ranked features of the feature selection procedure are fed as input to the base models of this this framework.  


1.LR_234_bands is the image file (PRISMA) which can be used as input to the PRISMA_band_selection_spatial_coherence_Renyi's_entropy.ipynb is available at 
https://drive.google.com/file/d/1-X8wUQS1LB5hx-6RCOQ_Fqwa3sYpvcBl/view?usp=sharing

2.Data_subset1.csv contains all the 191 bands after the statistical procedure of noisy bands removal.

3.This file should be used for feature importance calculation of bands corresponding to turbidity.

4.selected_features_dataset.csv consists the 5 bands with the highest feature importance and the turbidity column which can be used as input 
in SMOF. 
