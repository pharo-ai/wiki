# Measuring the accuracy of a model

To measure the accuracy of the model we will use the package metrics also available in Pharo-AI. You can refer to the metrics page of this wiki for more information.

For example, let's image that our logistic regression model has produced the following output.

```st
prediction := logisticRegressionModel predict: someValues
"#( 1 3 5 7 4 2)"
```

And we know that we the real values are:
```st
realValues := #(1 3.5 4 7 3.5 1)
```

So, we can test the accuracy of the model using the metrics that we just installed.
For the previous values, we can compute the r2 score (the coefficient of determination).

```st
prediction := #( 1 3 5 7 4 2).
realValues := #(1 3.5 4 7 3.5 1).

metric := AIR2Score new.
metric computeForActual: realValues predicted: prediction.
"0.8993288590604027"
```

So, our model has a r2 score 0.89. A R2 score of 1 indicates that the model is predicting perfectly.

For more information about please refer metrics wiki page available in this same wiki.