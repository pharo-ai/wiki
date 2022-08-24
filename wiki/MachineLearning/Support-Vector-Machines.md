<!--
    Plan for the blog post: 
    
    - Intro to the subject (SVM, soft margin, hard margin)
    - How to use it
    - Example using csv file and Dataframe (cancer, iris, ...)

-->

Repository: https://github.com/pharo-ai/Support-Vector-Machines


## Support Vector Machines

Support Vector Machines (abbreviated as SVM) are supervised learning algorithms that can be used for classification and regression problems. They are based on the idea of finding a hyperplane that best separates the features into two classes. <!--SVMs are one of the most robust prediction methods.-->Suppose that we have a set of points in a two-dimensional feature space, with each of the points belonging to one of two classes. An SVM finds what is known as a separating hyperplane: a hyperplane (a line, in the two-dimensional case) which separates the two classes of points from one another.

<p align="center">
<img src="./img/svmHyperplan.png" height="250" width="250" />
</p>

Depending on the dataset and how we want to classify it there are three approaches of SVMs: **Hard Margin**, **Soft Margin** and **Kernels**. We will introduce only the Soft Margin since it is the only implementation we have currently.
## Soft Margin

Soft Margin consists in classifying the data avoiding overfitting. In other words, we want to allow some misclassifications in the hope of achieving better generality.
Let's visualize this with an example : 

Given 4 input vectors _x = (x1, x2)_ and 4 output values _y_. 

```st
input := #( #( 1 0 0 ) #( 1 1 1 ) #( 1 3 3 ) #( 1 4 4 ) ).
output := #( 1 1 -1 -1 ).
```

You will notice that there's a 1 at the beginning of each row of the input, don't worry we will explain it later.

So we want to find a hyperplan that separates the two classes each input vector belongs to. SVM were originally designed to only support two-class-problems. So our two classes here are {-1,1}. As you can see, the red points belong to the first class and the blue ones to the second class.

<p align="center">
<img src="./img/1.png " height="250" width="250" />
</p>

We have an infinit number of possible hyperplans that can separate our data. Since we're looking for the best one we're dealing with an optimazation problem. Therefor, one of the commonly used ways of solving this type of problems is the Stochastic Gradient Descent (SGD) which will be used here. In simple words, SGD is an iterative learning process where an objective function is minimized according to the direction of steepest ascent so that the best coefficients for modeling may be converged upon.


<p align="center">
<img src="./img/2.png " height="250" width="250" />
<img src="./img/3.png " height="250" width="250" />
</p>



So basically the SVM what it does : it finds an optimal value of the parameter w (weight) and b (bias) such as when using the function **sgn(x)= sign(wx+b)** we manage to determine the classifier for our dataset. SVM is a linear model, hence the signe function is a straight line and has the general equation form. Where w is the gradient of the line (how steep the line is) and b is the y -intercept (the point in which the line crosses the y -axis).

For simplicity reasons, we  don't want to carry on a dot and a sum. We will push the param b into the weight vector and add a one at the end of each inputVector so that when operating the weights, the last input vector would be doted to the last weight instance and we can get the parameter b (bias).

```st
input := input collect: [ :each | each copyWith: 1 ].
```

We initialize an SVM model and fit it to the data. 

```st 
model := AISupportVectorMachines new.
model regularizationStrenght: 10000.
model learningRate: 1e-6.
model maxEpochs: 5000.

model fitX: inputMatrix y: outputVector.
```
Now that we trained the model, we have the values of the weights that will be needed to predict the output values for new input vectors. 

```st
model weights. "#(-0.5100145599113426 -0.5100145599113426 2.0303388323187965)"
```

The predict function is defined by the sign function

So can proceed to predict the output on a new input vector that we will name inputTaste

```st
testInput := #( #( 1 -1 -1 ) #( 1 5 5 ) #( 1 6 6 ) #( 1 7 7 ) ).
expectedOutput := #(1 -1 -1 -1)
```
```st
model predict: testInput  "#(1 -1 -1 -1)"
```
We can see that the expected output and the predicted one are the same.