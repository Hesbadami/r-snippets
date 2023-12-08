Setup
================

.. _libraries:

Libraries
----------------

.. code-block:: r
   
   library(tidyverse)  # for better data.frame manipulation
   library(dplyr)  # for easier transformations of the data.frame
   library(validate)  # for validation of data
   library(ggplot2)  # for visualization
   library(gridExtra)  # for grid plots
   library(modeest)  # for some statistical functions
   library(data.table)  # for making deep copies of dataframe
   library(xgboost)  # for xgboost
   library(caret)  # for xgboost

Reading Data
----------------

CSV:

.. code-block:: r

   data = read.csv('data.csv')

Rda:

.. code-block:: r
   
   load('data.csv')

Loading an `rda` ``Rda`` loads the data into a its default variable name.