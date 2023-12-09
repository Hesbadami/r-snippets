=====
Modeling
=====

Linear Regression
====
.. code-block:: r

    # Train the model
    model = lm(price ~ ., data = train)

Evaluation
------------
.. code-block:: r

    summary(model)
    plot(model

.. code-block:: r

    # Evaluate the Bias using Root Mean Squared Error metric
    y_pred = predict(model, train)
    y_train = train$price
    training_error = sqrt(mean((y_pred - y_train)**2))
    
    # Evaluate the Variance
    y_pred_valid_lm = predict(model, valid)
    y_valid_lm = valid$price
    validation_error_lm = sqrt(mean((y_pred_valid_lm - y_valid_lm)**2))
    
    cat("Training error", training_error)
    cat("Validation error", validation error)

.. code-block:: r

    colors    <- c( "c1" = "cornflowerblue", "c2" = "orange" )
    for (i in 1:length(num_col)) {
        x = num_col[i]
        if (x != "price") {
            figure = ggplot(
                test |> mutate(predicted_price = y_pred_test)
            ) +
            geom_point(aes(x = .data[[x]], y = price, color = "c1")) +
            geom_point(aes(x = .data[[x]], y = predicted_price, color = "c2")) +
            labs(title = sprintf("Scatter plot of price vs %s", x)) +
            scale_color_manual( 
            breaks = c("c1", "c2"), 
            values = colors,
            labels = c("Price", "Predicted Price"))
    
            suppressMessages(print(figure))
        }
    }

Logistic Regression
====
.. code-block:: r

    # Train the model
    model = glm(first_owner ~ ., data = train, family = binomial)

Evaluation
------------
.. code-block:: r

    summary(model)
    plot(model

.. code-block:: r

    # Evaluate the Bias
    y_pred = predict(model, train, type = "response")
    y_train = train$first_owner
    training_error = logloss(y_pred, y_train)
    
    # Evaluate the Variance
    y_valid = valid$first_owner
    y_pred_valid_lr = predict(model, valid, type = "response")
    validation_error_lm = logloss(y_pred_valid_lr, y_valid)

    cat("Training error", training_error)
    cat("Validation error", validation error)
