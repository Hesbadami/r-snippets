Setup
====

.. _libraries:

Libraries
----------------

.. code-block:: R
   library(tidyverse)  # for better data.frame manipulation
   library(dplyr)  # for easier transformations of the data.frame
   library(validate)  # for validation of data
   library(ggplot2)  # for visualization
   library(gridExtra)  # for grid plots
   library(modeest)  # for some statistical functions
   library(data.table)  # for making deep copies of dataframe
   library(xgboost)  # for xgboost
   library(caret)  # for xgboost

Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

