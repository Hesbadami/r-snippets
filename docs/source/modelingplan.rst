Modeling Plan
=====

.. code-block:: md

    1. Preprocessing. 
        * Create dummy variables for non ordinal categorical columns (One hot encode). because we only can use numerical values as linear regression model, and we cannot rescale factors.
        * Label encode ordinal categorical variables for the same reason.
        * Split the data set to three sets: training set, validation set, and test set so that we can evaluate our model on unseen data and so that we can use the validation set as our playground and still have a test set to evaluate our final model on, to make sure our fine tunings doesn't cause a manual overfit.
        * Scale all the features using a Standard Scaler. This standardizes the features by removing the mean and scaling to unit variance. The standard score of a sample x is calculated as: $z = (x - \mu) / s$. Using a MinMax Scaler is not recommended since the upper bound for some of the features is unknown.
    
    
    2. Select models; the following models are going to be used:
        * Linear regression; a simple standard approach that provides a reasonable estimate for future values to predict.
    
    
    3. Train each model on the Training set, then examine the Bias (training error) to see if we have a large bias, suggesting underfit.
    
    
    4. Estimate the validation error by predicting values from the Validation set. Examine the difference between this error and the training error (AKA Variance) to provide diagnosis. If it is too high, it implies overfitting.


    5. Attempt to improve the model via further simplifications and the step by step removal of insignificant columns, to see if it improves validation error.
    

    6. Select the best model (with the lowest Bias and Variance) and test it using the Test set to provide a model critique.

