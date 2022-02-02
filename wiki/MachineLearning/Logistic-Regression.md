
# Logistic regression

Repository: https://github.com/pharo-ai/linear-models

Logistic regression is a statistical model that predicts the likehood that an event can happen giving as an output the probability.

For example, if we have a function: 

```
 f(x) = 1   if x ≥ 0
        0   if x < 0
```

that returns `1` for all positive numbers including 0, and `0` for all negative numbers.
We can train a logistic model for making predictions when a new number arrives.

```st
input := #( (-6.64) (-7) (-0.16) (-8) (-2)
    (-90.03) (-7.26) (-3.02) (-54.18) (-9.6)
    (8) (7) (3.29) (8.21) (76.09)
    (45) (3) (10) (9) (5.11)).

output := #(0 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1).
```

Now we have to train the logistic regression model.

```Smalltalk
logisticRegressionModel := AILogisticRegression new
  learningRate: 0.001;
  maxIterations: 2000;
  yourself.
	
logisticRegressionModel fitX: input y: output.
```

We can look at the trained parameters.

```Smalltalk
b := logisticRegressionModel bias.
"0.005920279313031135"
w := logisticRegressionModel weights.
"#(0.9606463585049467)"
```

We can use the model to predict the output for previously unseen input.

```Smalltalk
testInput := #( (-3) (-0.11) (-4.67) (9) (7) (10)).
    
expectedOutput := #(0 0 0 1 1 1).
```

```Smalltalk
logisticRegressionModel predict: testInput.
"#(0 0 0 1 1 1)"
```

If we want to have the probabilities of the model, not just `1` or `0`, we can call the method `predictProbabilities:`. It will return us the probabilities that the input has an output of 1.

```st
logisticRegressionModel predictProbabilities: testInput
"#(0.05335185163762839 0.4750829523986599 0.011203102336961287 0.9998252077292514 0.998807422178672 0.9999331093095885)"
```

In our example, we have a `0.05335185163762839` probability that the output for `-3`  is `1`. Also we have a `0.9999331093095885` probability that the output for `10` is `1`.

[Measuring the accuracy of a model](./Measuring-the-accuracy-of-a-model.md)