Preprocessing
=====

One Hot Encoding
----------
.. code-block:: r

    # Function to create dummy variables
    to_dummy = function(df, cols = c()){
        df = copy(df)
        for (col in colnames(df)){
            if ((!is.numeric(df[[col]])) & ((col %in% cols) | length(cols) == 0)){
                unique_values = unique(df[[col]])
                for (v in unique_values[1:(length(unique_values)-1)]){
                    colname = paste(col, v, sep="_")
                    df[[colname]] = as.logical(ifelse(df[[col]] == v, 1, 0))
                }
                df = df |> select(-col)
            }
        }
        return (df)
    }

    # Create dummy variables
    df_dummy = to_dummy(df)


Rescaling
----------
.. code-block:: r
   
    # Scale data
    StandardScaler = function(df, cols = c()){
        df = copy(df)
        for (col in colnames(df)){
            if ((col %in% cols) | length(cols) == 0){
                df[[col]] = (df[[col]] - mean(df[[col]]))/sd(df[[col]])
            }
        }
        return (df)
    }

    # Scale the data
    df_scaled = StandardScaler(df_dummy)


Splitting
----------
.. code-block:: r

    # Train Test Split function
    train_test_split = function(df, test_size=0.2){
        N = nrow(df)
        split = floor((1-test_size)*N)
        df = copy(df)
        train = df[1:(split-1), ]
        test = df[split:N, ]
        
        return (
            list(
                train = train,
                test = test
            )
        )
    }
    
    # Split the dataset to Training_Validation and Test set
    traintestsplit = train_test_split(df_scaled, test_size = 0.3)
    train_valid = traintestsplit$train
    test = traintestsplit$test
    
    # Split again to Training and Validation set 
    trainvalidsplit = train_test_split(train_valid, test_size = 0.3)
    train = trainvalidsplit$train
    valid = trainvalidsplit$test

