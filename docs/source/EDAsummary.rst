EDA Summary
=====

.. code-block:: md

    ##### Interesting patterns:
    
    **Uni-variate analysis**
    1. `weight` is normally distributed but `age` is not, as observed in the shapiro test results.
    2. None of the categorical variables seem to be dominated by a specific value, and look equally distributed.
    
    
    **Multi-variate analysis**
    1. Significant correlation is observed between the following pairs, all of which follow our expectations of reality:
        * `year` vs `price`: 0.58
        * `engine_size` vs `price`: 0.27
        * `mileage` vs `price`: -0.62
        * `year` vs `mileage`: -0.56
        * `engine_size` vs `min_mpg`: -0.36
        * `engine_size` vs `max_mpg`: -0.25
        
        
    2. Top 5 significant relationships between the pairs of categorical variables:
        * `navigation_sytem` vs `heated_seats`; As observed in the stacked bar plots, 70% of the cars with heated seats, also have navigation system.
        * `automatic_transmission` vs `bluetooth`; ~90% of automatically handled cars have bluetooth.
        * `automatic_transmission` vs `navigation_system`; A car with navigation system is 3 times more likely to be automatic.
        * `third_row_seating` vs `heated_seats`; ~75% of cars with third row seating, also have heated seats.
        * `navigation_system` vs `bluetooth`; cars that offer bluetooth are ~3 times more likely to have a navigation system.
        
        
    3. And judging by the results of Anova test and box plots, the top 5 significant patterns for categorical vs numerical are:
        * `bluetooth` vs `year`
        * `first_owner` vs `mileage`
        * `navigation_system` vs `price`
        * `fuel` vs `max_mpg`
        * `automatic_transmission` vs `year`
        
        
    4. Judging by Ancova test results and as seen in the visualizations, the top 5 numerical correlations affected by categorical variables are as follows:
        * `price` vs `mileage`/`drivetrain`
        * `price` vs `mileage`/`navigation_system`
        * `price` vs `year`/`drivetrain`
        * `price` vs `year`/`navigation_system`
        * `price` vs `mileage`/`third_row_seats`

