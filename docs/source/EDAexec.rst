=====
EDA Execution
=====
.. code-block:: r

    num_col = c('weight', 'age')
    cat_col = c('breed', 'sex')

Uni-variate Exploration
====

Numerical
------------

**Histogram & density plot**

.. code-block:: r

    hist_plots = list(nrow=2)
    for (col in num_col) {
        hist_plots[[col]] = ggplot(
            df,
            aes(x = .data[[col]], y = after_stat(density))
        ) +
            geom_histogram(color='white', fill='cornflowerblue', bins=ceiling(sqrt(nrow(df)))) +
            geom_density(fill='cornflowerblue', alpha=0.2) +
            labs(title = col)
    }

    do.call(grid.arrange, hist_plots)

**Normality test**

.. code-block:: r

    Shapiro_results = as.data.frame(sapply(df[num_col], function(col) {return (c(shapiro.test(col)))}))[1:3,]
    Shapiro_results

.. code-block:: r

    for (col in num_col) {
        qqnorm(
            df[[col]],
            main = paste("Normal Q-Q Plot for", col)
        )
        qqline(df[[col]])
    }

**Statistics**

.. code-block:: r

    return_stats = function(col) {
        return (
            c(
                round(IQR(col), 2),
                round(range(col), 2),
                round(mean(col), 2),
                round(median(col), 2),
                round(sd(col), 2),
                round(var(col), 2)
            )
        )
    }

    result = sapply(df[num_col], return_stats)
    row.names(result) = c('IQR', 'Range_Start', 'Range_End', 'Mean', 'Median', 'Std', 'Variance')
    result

Categorical
------------

**Bar plot**

.. code-block:: r

    bar_plots = list(nrow=1)
    for (col in cat_col) {
        bar_plots[[col]] = ggplot(
            df,
            aes(x = .data[[col]])
        ) +
            geom_bar(color='white', fill='cornflowerblue') +
            labs(title = col) +
            theme(axis.text.x = element_text(
                angle = ifelse(
                    any(sapply(as.vector(unique(df[[col]])), nchar) > 5),
                    90,
                    0
                ), vjust = 0.5, hjust=1)
            )
    }

    do.call(grid.arrange, bar_plots)

**Mode**

.. code-block:: r

    Mode = sapply(df[cat_col], function(col) {return (c(mfv(col)))})
    as.data.frame(x = Mode)


Multi-variate Exploration
====

Numerical *vs.* numerical
------------

**Scatterplot**

.. code-block:: r
    
    for (i in 1:length(num_col)) {
        for (j in i:length(num_col)) {
            ys = num_col[i]
            xs = num_col[j]
            if (xs != ys) {
                figure = ggplot(
                    df,
                    aes(x = .data[[xs]], y = .data[[ys]])
                ) +
                geom_point() +
                geom_smooth(method = "lm", color = 'red', formula='y~x') +
                labs(title = sprintf("Scatter plot of %s vs %s", ys, xs))
            
                suppressMessages(print(figure))
            }
        }
    }

**Correlation test**

.. code-block:: r

    cor_tests = list()
    
    for (i in 1:length(num_col)) {
        for (j in i:length(num_col)) {
            y = num_col[i]
            x = num_col[j]
            if (x != y) {
                test_result = cor.test(df[[y]], df[[x]])
                cor_tests[[paste(y, "vs", x, sep="_")]] = c(test_result$p.value, test_result$estimate[["cor"]])
            }
        }
    }
    
    cor_results_table = as.data.frame(t(data.frame(cor_tests, row.names = c('p-value', 'correlation')))) |> arrange(desc(correlation))
    cor_results_table

Categorical *vs.* categorical
------------

**100% stacked bar plot**

.. code-block:: r

    for (i in 1:length(cat_col)) {
        for (j in i:length(cat_col)) {
            xs = cat_col[i]
            ys = cat_col[j]
            if (xs != ys) {
                p1 = ggplot(
                    df |>
                        count(.data[[xs]], .data[[ys]]) |>
                        group_by(.data[[xs]]) |>
                        mutate(pct = prop.table(n) * 100),
                    aes(x = .data[[xs]], y = pct, fill = .data[[ys]])
                ) +
                geom_bar(stat="identity") +
                theme(
                    axis.text.x = element_text(
                        angle = ifelse(
                            any(sapply(as.vector(unique(df[[xs]])), nchar) > 5),
                            90,
                            0
                        ), vjust = 0.5, hjust=1
                    )
                ) +
                labs(title = sprintf("Stacked bar plots for %s vs %s", xs, ys), y = "Percentage")
            
                p2 = ggplot(
                    df |>
                        count(.data[[xs]], .data[[ys]]) |>
                        group_by(.data[[ys]]) |>
                        mutate(pct = prop.table(n) * 100),
                    aes(x = .data[[ys]], y = pct, fill = .data[[xs]])
                ) +
                geom_bar(stat="identity") +
                theme(
                    axis.title.y = element_blank(),
                    axis.text.x = element_text(
                        angle = ifelse(
                            any(sapply(as.vector(unique(df[[ys]])), nchar) > 5),
                            90,
                            0
                        ), vjust = 0.5, hjust=1
                    )
                ) +
                labs(title = sprintf("/ %s vs %s", ys, xs))
                
                grid.arrange(p1, p2, nrow=1)
            }
        }
    }

**ChiSq / Fisher test**

.. code-block:: r

    chi_fisher_tests = list()
    
    for (i in 1:length(cat_col)) {
        for (j in i:length(cat_col)) {
            y = cat_col[i]
            x = cat_col[j]
            if (x != y) {
                table_ = table(df[[y]], df[[x]])
                chi_fisher_tests[[paste(y, "vs", x, sep="_")]] = tryCatch(
                    {c(chisq.test(table_)$p.value)},
                    warning = function(w) {
                        test_result = fisher.test(df[[x]], df[[y]])
                        return(
                            c(test_result$p.value)
                        )
                    }
                )
            }
        }
    }
    
    chi_fisher_results_table = as.data.frame(t(as.data.frame(chi_fisher_tests, row.names = "p-value"))) |> arrange(.data[["p-value"]])
    chi_fisher_results_table


Numerical *vs.* categorical
------------

**Boxplot**

.. code-block:: r

    for (i in 1:length(num_col)) {
        for (j in 1:length(cat_col)) {
            xs = cat_col[j]
            ys = num_col[i]
            if (xs != ys) {
                figure = ggplot(
                    df,
                    aes(x = .data[[xs]], y = .data[[ys]])
                ) +
                geom_boxplot() +
                labs(title = sprintf("Box plot of %s vs %s", ys, xs))
            
                suppressMessages(print(figure))
            }
        }
    }

**ANOVA**

.. code-block:: r

    aov_tests = list()
            
    for (i in 1:length(num_col)) {
        for (j in 1:length(cat_col)) {
            x = cat_col[j]
            y = num_col[i]
            if (x != y) {
                aov_tests[[paste(y, "vs", x, sep="_")]] = summary(aov(df[[y]] ~ df[[x]]))[[1]][["Pr(>F)"]][1]
            }
        }
    }
    
    anova_results_table = as.data.frame(t(as.data.frame(aov_tests, row.names = "Pr(>F)"))) |> arrange(.data[["Pr(>F)"]])
    anova_results_tablea

**ANCOVA**

.. code-block:: r

    ancova_tests = list()
    
    extract_p <- function(my_model) {
        f <- suppressWarnings(summary(my_model)$fstatistic)
        p <- pf(f[1],f[2],f[3],lower.tail=F)
        attributes(p) <- NULL
        return(p)
    }
    
    for (i in 1:length(num_col)) {
        for (j in 1:length(cat_col)) {
            for (k in i:length(num_col)) {
                y = num_col[i]
                x1 = cat_col[j]
                x2 = num_col[k]
                if (x2 != y) {
                    ancova_tests[[paste(y, "vs", x1, "and", x2, sep="_")]] = extract_p(lm(df[[y]] ~ df[[x1]] + df[[x2]]))
                }
            }
        }
    }
    
    ancova_results_table = as.data.frame(t(as.data.frame(ancova_tests, row.names = "p-value"))) |> arrange(.data[["p-value"]])
    ancova_results_table

**Interaction scatter plot**

.. code-block:: r

    for (i in 1:length(num_col)) {
        for (j in 1:length(cat_col)) {
            for (k in i:length(num_col)) {
                y = num_col[i]
                x1 = cat_col[j]
                x2 = num_col[k]
                
                if (y != x2) {
                    figure = ggplot(
                        df,
                        aes(y=.data[[y]], x=.data[[x1]], color=.data[[x2]])
                    ) +
                    geom_point() +
                    geom_smooth(method = 'lm', formula = 'y~x')
    
                    suppressWarnings(print(figure))
                }
            }
        }
    }

