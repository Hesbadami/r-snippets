Data Quality
=====

Quality Check Plan
------------
.. code-block:: md

   1. `str` can be used to identify if any variable has an incorrect data type. 
      * `xray`, `yankee`, `zulu` should be character.
      * `alpha`, `bravo`, `charlie` should be integers.
      * `golf`, `hotel`, `juliett` should be double float number.
      * `quebec`, `whiskey`, `echo` should be factor.


   2. `summary` and `validator` offer insight on missing values and invalid values. Possible invalid or implausible values are:
      * Non positive `alpha`, `golf`.
      * Positive `juliett`, `bravo`.
      * Non integer values in `alpha`, `bravo`
      * `bravo`, `charlie` more than 1024.
      * `quebec` not in *quebec1*, *quebec2*, *quebec3*.
      * `alpha` more than 128 for `golf` less than 128.
      * `alpha` less than 128 for `golf` more than 128.


      Categorical variables can also be inspected for typos (similar categories that for example need to be stripped of whitespaces) or missing values using `unique`.


   3. Outliers are identified using boxplots.


   4. Duplicate rows are checked.

Quality Check Execution
----------------
.. code-block:: r

   str(df)

.. code-block:: r

   summary(df)

.. code-block:: r

   unique(df$quebec)
   unique(df$whiskey)
   unique(df$echo)

.. code-block:: r

   rules = validator(
      alpha > 0,
      golf > 0,
      bravo < 1024,
      charlie < 1024,
      round(age) == age,  # should be integer
      quebec %in% c('quebec1', 'quebec2', 'quebec3'),
      ((alpha <= 128) | (golf >= 128)), # alpha cannot be more than 128 unless golf is more than 128
      ((alpha >= 128) | (golf <= 128)), # alpha cannot be less than 128 unless golf is less than 128
   )

   cf = confront(df, rules)
   summary(cf)
   plot(cf)

.. code-block:: r

   count = sum(duplicated(df))
   cat(count, "duplicates observed")

.. code-block:: r

   get_boxplots = function(df){
      plots = list(nrow=2)
         for (col in colnames(df)) {
               if (is.numeric(df[[col]])){
                  plots[[col]] = ggplot(
                     df,
                     aes(y = .data[[col]])
                  ) +
                     geom_boxplot() +
                     labs(title = col) + 
                     theme(
                           axis.text.x=element_blank(),
                           axis.ticks.x=element_blank()
                     )
               }
         }
      return plots
   }

   boxplots = get_boxplots(df)
   do.call(grid.arrange, boxplots)

Quality Check Findings
----------------
.. code-block:: md

   1. While inspecting data types, multiple issues were discovered as follows:
      * `xray`, `yankee`, `zulu` are character.
      * `alpha`, `bravo`, `charlie` are integers.
      * `golf`, `hotel`, `juliett` are double float number.
      * `quebec`, `whiskey`, `echo` are factor.


   2. 256 *NA* values were observed in `whiskey`, `hotel`.


   Multiple invalid values were also found as follows:
      * 32 non positive values in `alpha`, `golf`.
      * 64 positive values in `juliett`, `bravo`.
      * 8 values above 1024 in `bravo`, `charlie`.
      * `age` has a value which is not integer.
      * Category "qebec2" looks like a typo for `quebec`.
      * `alpha` has 16 invalid values too high for it's corresponding `golf`.
      * `alpha` has 4 invalid values too low for it's corresponding `golf`.


   3. 32 outliers were identified in `bravo`, `charlie`.


   4. 512 duplicate rows were identified.

Cleaning Plan
----------------
.. code-block:: md

   We should drop the rows containing non integer values for age before fixing it's data type to integer because then they would be rounded to 0 and lost.

   1. Data types should be modified as follows:
      * Data types for `xray`, `yankee`, `zulu` should change to character.
      * Data types for `alpha`, `bravo`, `charlie` should change to integers.
      * Data types for `golf`, `hotel`, `juliett` should change to double float number.
      * Data types for `quebec`, `whiskey`, `echo` should change to factor.


   2. Rows containing invalid should be addressed as follows:
      * The 32 non positive values in `alpha`, `golf` should have their rows dropped.
      * The 64 positive values in `juliett`, `bravo` should have their rows dropped.
      * The 8 values above 1024 in `bravo`, `charlie` should have their rows dropped.
      * Category "qebec2" should be replaced with "quebec2" for `quebec`.
      * The 20 invalid values in `alpha` and `golf` shohuld have their rows dropped.

   
   3. *NA* values can be addressed as follows:
      * Some of the missing `whiskey` values correspond to `hotel` value "hotel1", which we will manually set to 0 to help our model distinguish them.
      * Some of the missing `hotel` values correspond to `quebec` value "quebec1", which we will manually set to `hotel_unknown` to help our model distinguish them.
      * `whiskey` can be grouped by temporary variable `golf.range` [Reason] and `echo` [Reason] and imputed with the mean of each group.
      * `hotel` can be grouped by temporary variable `golf.range` [Reason] and `echo` [Reason] and imputed with the mode of each group.

   
   4. Outliers from `bravo`, `charlie` should be removed, if our model doesn't get affected negatively. We'll test this sensitivity by training a linear regression on both cases and comparing the results from before and after.


   5. Duplicate rows should be dropped.

Cleaning Execution
----------------
.. code-block:: r
   # drop the rows containing non integer `age`
   df = df[round(df$age) == df$age,]

.. code-block:: r

   # `xray`, `yankee`, `zulu` to character.
   for (col in c('xray', 'yankee', 'zulu')) {
      df[[col]] = as.character(df[[col]])
   }

   # `alpha`, `bravo`, `charlie` to integers
   for (col in c('alpha', 'bravo', 'charlie')) {
      df[[col]] = as.integer(df[[col]])
   }

   # `golf`, `hotel`, `juliett` to double float number.
   for (col in c('golf', 'hotel', 'juliett')) {
      df[[col]] = as.numeric(df[[col]])
   }

   # `quebec`, `whiskey`, `echo` to factor
   for (col in c('quebec', 'whiskey', 'echo')) {
      df[[col]] = as.factor(df[[col]])
   }

.. code-block:: r

   df = df |> mutate(quebec = recode(quebec, 'qebec2' = 'quebec2')) 
   df = df |> filter(
      alpha > 0,
      golf > 0,
      bravo < 1024,
      charlie < 1024,
      ((alpha <= 128) | (golf >= 128)),
      ((alpha >= 128) | (golf <= 128))
   )
   
.. code-block:: r

   # Data is grouped by golf.range and echo
   # whiskey missing values are replaced by the mean of each group.
   df = df |>
      mutate(golf.range = cut(golf, breaks = 5)) |>
      group_by(golf.range, echo) |>
      mutate(
         whiskey = replace_na(
               whiskey,
               mean(whiskey, na.rm = TRUE)
         )
      ) |>
      ungroup() |>
      select(-golf.range)
      
      )

   # We set the ones belonging to "hotel1" from hotel to 0. We do this after imputation so that it doesn't affect the mean values.
   df = df |> mutate(whiskey = ifelse(
      hotel == 'hotel1',
      0,
      whiskey
   ))

   # For any remaining NA values due to a group missing all values, we replace with the mean of the whole column.
   df = df |> mutate(whiskey = replace_na(whiskey, mean(whiskey, na.rm = TRUE)))
   
.. code-block:: r

   # Data is grouped by golf.range and echo
   # hotel missing values are replaced by the mode of each group.
   df = df |>
      mutate(golf.range = cut(golf, breaks = 5)) |>
      group_by(golf.range, echo) |>
      mutate(
         hotel = replace_na(
               hotel,
               mfv(hotel, na_rm = TRUE)[1]
         )
      ) |>
      ungroup() |>
      select(-golf.range)

   # We set the ones belonging to "quebec1" from quebec to "hotel_unknown". We do this after imputation in case they consist the majority of the column.
   df = df |> mutate(hotel = ifelse(
      quebec == 'quebec1',
      'hotel_unknown',
      hotel
   ))

   # For the remaining NA due to a group missing all values, we replace with the mean of the whole column.
   df = df |> mutate(hotel = replace_na(hotel, mfv(hotel, na_rm = TRUE)[1]))

   df$hotel = as.factor(df$hotel)  # to perserve data types and remain class-safe
   
.. code-block:: r

   not_outlier_mask = 1
   for (col in c('bravo', 'charlie')){
      not_outlier_mask = not_outlier_mask * (
         df[[col]] > quantile(df[[col]], 0.25) - 1.5*IQR(df[[col]])
      ) * (
         df[[col]] < quantile(df[[col]], 0.75) + 1.5*IQR(df[[col]])
      )
   }

   # data w/o outliers
   df_woo = df[as.logical(not_outlier_mask), ]

   # model w/ outliers
   model_o = lm(juliett ~ bravo + charlie, df)
   summary(model_o)
   
   # model w/o outliers
   model_woo = lm(jupiett ~ bravo + charlie, df_woo)
   summary(model_woo)

.. role:: rinline(code)
   :language: r

If the model is not affected negatively, we're allowed to remove the outliers;    :rinline:`df = df_woo`.
   
.. code-block:: r

   df = df[!duplicated(df), ]
