EDA Plan
=====
.. code-block:: md

    Dependant and Explanatory variables

    * Explanatory: `breed`, `sex` and `age`
    * Dependant: `weight`
    * The relationship between every combination of variables, including the above, is explored via statistical tests and visualizations, in the following sections.\


    1. Uni-variate Exploration 
        * Numerical:
            * **Histogram & density plot** to observe the distribution, skewness and normality.
            * `shapiro.test` for testing normality and visualizing using **Q-Q plots**.s
            * *range*, *IQR*, *mean*, *median*, *std* and *variance*, for further insight on the data, and wether we need to rescale them later before modeling.
        * Categorical:
            * **Bar plot** to represent the count for each category.
            * mode using `mfv`.
    

    2. Multi-variate Exploration
        * Numerical *vs.* numerical:
            * **Scatterplot**
            * `cor.test` to test how they covary with each other.
        * Categorical *vs.* categorical:
            * **100% Stacked bar plot** useful for visually representing the proportional contribution of different categories.
            * `chisq.test` (`fisher.test`) for relationsihp between categorical variables.
        * Numerical *vs.* categorical:
            * **Boxplot** to visualize how the numerical mean varies within different categories of the categorical variable.
            * **ANOVA** (using `aov`) to statisticaly test the trends observed.
            * **ANCOVA** (using `lm`) for any combination of numerical and categorical variables.
            * **interaction scatter plot** used in the context of ANCOVA to visually represent the relationships.