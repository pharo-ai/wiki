# Linear regression

Repository: https://github.com/pharo-ai/linear-models

Linear regression is a well-known machine learning algorithms.
It allows us to learn the linear dependency between the input variables $x_1, \dots, x_n$ and the output variable $y$.
Attempts to find the the linear relationship between one or more input variables _x1, x2, ..., xn_ and an output variable _y_. It finds a set of parameters _b, w1, w2, ..., wn_ such that the predicted output _h(x) = b + w1 * x1 + ... + wn * xn_ is as close as possible to the real output _y_. Then we can use the trained model to predict the previously unseen values of y.

For example:

Given 20 input vectors _x = (x1, x2, x3)_ and 20 output values _y_.

```Smalltalk
input := #( (-6 0.44 3) (4 -0.45 -7) (-4 -0.16 4) (9 0.17 -8) (-6 -0.41 8)
    (9 0.03 6) (-2 -0.26 -4) (-3 -0.02 -6) (6 -0.18 -2) (-6 -0.11 9)
    (-10 0.15 -8) (-8 -0.13 7) (3 -0.29 1) (8 -0.21 -1) (-3 0.12 7)
    (4 0.03 5) (3 -0.27 2) (-8 -0.21 -10) (-10 -0.41 -8) (-5 0.11 0)).

output := #(-10.6 10.5 -13.6 27.7 -24.1 12.3 -2.6 -0.2 12.2 -22.1 -10.5 -24.3 2.1 14.9 -11.8 3.3 1.3 -8.1 -16.1 -8.9).
```

We want to find the linear relationships between the input and the output. In other words, we need to find such parameters _b, w1, w2, w3_ that the line _h(x) = b + w1 * x1 + w2 * x2 + w3 * x3_ fits the data as closely as possible. To do that, we initialize a linear regression model and fit it to the data.

```Smalltalk
linearRegressionModel := AILinearRegression new
  learningRate: 0.001;
  maxIterations: 2000;
  yourself.

linearRegressionModel fitX: input y: output.
```

Now we can look at the trained parameters. The real relationship between x and y is _y = 2*x1 + 10*x2 - x3_, so the parameters should be close to _b=0_, _w1=2_, _w2=10_, _w3=-1_.

```Smalltalk
b := linearRegressionModel bias.
"-0.0029744215186773065"
w := linearRegressionModel weights.
"#(1.9999658061022905 9.972821149946537 -0.9998757756318858)"
```

Finally, we can use the model to predict the output of previously unseen input.

```Smalltalk
testInput := #( (-3 0.43 1) (-3 -0.11 -7) 
    (-6 0.06 -9) (-7 -0.41 7) (3 0.43 10)).
    
expectedOutput := #(-2.7 -0.01 -2.4 -25.1 0.3).
```

```Smalltalk
linearRegressionModel predict: testInput.
"#(-2.7144345209804244 -0.10075173689646852 -2.405518008448657 -25.090722165135997 0.28647833494634645)"
```

## Measuring the accuracy of a model

Please refer to the wiki for [Measuring the accuracy of a model](./Measuring-the-accuracy-of-a-model.md)

## Least Squares + Lapack

We have implemented the class `AILinearRegressionLeastSquares` that also trains a linear regression model but using other approach. The class `AILinearRegression` uses the gradient descent algorithm for finding the weights of the model. With the class `AILinearRegressionLeastSquares` we model the problems in terms of the least squares problem and we solve it using [Linear Algebra](../LinearAlgebra/Lapack.md). It's a package for solving linear algebra problem, like least squares.That library uses [Pharo-Lapack](../LinearAlgebra/Lapack.md), that is a binding for Lapack in Pharo. We use Lapack for speed up the computation, as Lapack it's a highly optimized library written in Fortran.

The prerequisite is that you need to have Lapack installed on your computer. If you use a Mac, you have it already installed by default. You can check [this link](https://netlib.org/lapack/lug/node14.html) for installing it.

### How to use it

This class has the same API for training and predicting as the other ones. 

```st
linearRegressionModel := AILinearRegressionLeastSquares new.

linearRegressionModel fitX: input y: output.
linearRegressionModel predict: testInput.
```

The difference is that you need to use the class `AIColumnMajorMatrix` or even `AINativeFloatMatrix`. Those are matrices data structures that we implemented meant to be used with Lapack. They are optimized to avoid transposing the matrix and copying the data.

You can create the matrices using the class side message `rows`


```st
AIColumnMajorMatrix rows: (
    (12  3  5)
    (1   4  17)
    (3   7  33)
).

AINativeFloatMatrix rows: (
    (12  3  5)
    (1   4  17)
    (3   7  33)
).
```

For more information, please refer to the wiki pages [Linear Algebra](../LinearAlgebra/LinearAlgebra.md) and [Lapack](../LinearAlgebra/Lapack.md)


### Least Squares using PolyMath

We also have the class `AILinearRegressionLeastSquaresVanilla` that is a subclass of `AILinearRegressionLeastSquares`. But it uses PolyMath that is written in pure Pharo. You can call the fit method with an array of array, as it doesn't use Lapack, you don't need the special data structures.
