# Support Vector Machines tutorial


_If you don't have the library installed, you can refer to: [Getting Started page](../GettingStarted/GettingStarted.md)_




Packages to load :

```st
Metacello new
  baseline: 'AIRandomPartitioner';
  repository: 'github://pharo-ai/RandomPartitioner';
  load.

Metacello new
  baseline: 'AIDatasets';
  repository: 'github://pharo-ai/datasets';
  load.
 
Metacello new
  baseline: 'AISupportVectorMachines';
  repository: 'github://pharo-ai/support-vector-machines';
  load.

Metacello new
  baseline: 'AINormalization';
  repository: 'github://pharo-ai/normalization';
  load.
```
Support Vector Machines (abbreviated as SVM) are supervised learning algorithms that can be used for classification and regression problems. They are based on the idea of finding a hyperplane that best separates the features into two classes.

We will use data from the [Wisconsin breast cancer diagnostic dataset](https://www.kaggle.com/code/buddhiniw/breast-cancer-prediction) to train the machine learning to be able to predict if someone has or not diabetes based in several parameters.

The Wisconsin dataset contains 32 parameters of a breast cancer, like its radius mean, its area mean, its smoothness mean, etc and as an output the dignosis of the cancer wether it's begnine ou maligne.

First, we will load the dataset. Then, normalizing the data, to split between test and training datasets. After that, we will train the SVM model and finally measuring its performance by predicting on test data.

## Preprocessing the data

We will use [Pharo Datasets](https://github.com/pharo-ai/datasets) to load the dataset into the Pharo image. The library contains several datasets ready to be loaded. Pharo Datasets will return a [Pharo DataFrame](https://github.com/PolyMathOrg/DataFrame) object. To install Pharo Datasets you only need to run the code sniped of the Metacello script available on the [README](https://github.com/pharo-ai/Datasets)

First, we load the boston housing dataset into the Pharo image.

```st
"Loading the dataset"
data := AIDatasets loadBreastCancer.
```

Now, to train the machine model we need to separate the dataset into at least two parts: one for training and the other for testing it. We have already a library in Pharo that does that: [Random partitioner](https://github.com/pharo-ai/random-partitioner). It is already included be default if you load the Pharo Datasets library.

We will separate it into two sets: test and train. We need this to train the model and after to see how precisely the model is predicting.

```st
"Dividing into test and training"
partitioner := AIRandomPartitioner new.
subsets := partitioner split: data withProportions: #(0.75 0.25).
trainData := subsets first.
testData := subsets second.
```

Then, we need to obtain the X (independent, input variables) and Y (dependent, output variable) for each one of the test and training sets.

```st
"Separating between X and Y"
trainData columnNames. "an OrderedCollection('RADIOUS_MEAN' ..)"

xTrain := trainData columns: #('RADIOUS_MEAN' .. ).
yTrain := trainData column: 'DGNCNCR'.

xTest := testData columns: #('RADIOUS_MEAN' .. ).
yTest := testData column: 'DGNCNCR'.
```

Finally, as our SVM model only accepts `SequenceableCollection` and not `DataFrame` objects (for now!), we need to convert the DataFrame into an array. We can do that only sending the message `asArray` or `asArrayOfRows`.

```st
"Converting the DataFrame into an array of arrays For using it in the linear model.
For now, the linear model does not work on a DataFrame."
xTrain := xTrain asArrayOfRows.
yTrain := yTrain asArray.

xTest := xTest asArrayOfRows.
yTest := yTest asArray.
```

# Training the machine learning model

We have everything that is needed to start training the SVM model. We need to load the [Support Vector MAchines library](https://github.com/pharo-ai/support-vector-machines from pharo-ai.

We instantiate the model, set the regularizationStrenght, the learning rate and the max iterations (epochs). After that, we train the model with the `trainX` and `trainY` collections that we have obtained.



```st 
"Training the SVM model"

model := AISupportVectorMachines new.
model regularizationStrenght: 10000.
model learningRate: 1e-6.
model maxEpochs: 5000.

model fitX: xTrain y: yTrain.
```

## About normalization

### What is normalization?

In statistics and machine learning, normalization is the process which transforms multiple columns of a dataset to make them numerically consistent with each other (e.g. be on the same scale) but at the same time preserve the valuable information stored in these columns.

For example, we have a table containing the Salaries that a person earns according to some criteria. The values of variable Years since PhD are in the range of `[1 .. 56]` and the salaries `[57,800 .. 231,545]`. If we plot the two variables we see:

<img src="img/normalization_comparison.png"  width=650 height=350 alt="Source: https://blog.oleks.fr/normalization">

So, the big difference between the range of the values can affect our model.

If you want to read more about normalization Oleks has a [nice blog post](https://blog.oleks.fr/normalization) about it.
>Part of the text for explaining normalization were extracted from that post.

For normalizing our data, DataFrame has a simple API: we just call the `DataFrame >> normalized` method that returns a new DataFrame that has been normalized. This method uses the default normalizer that is the min max normalizer. If you want to use another one you can use the method `DataFrame >> normalized: aNormalizerClass` instead.

So, we just execute this part **before** partitioning the data.

```st
"Normalizing the data frames"
normalizedData := data normalized.
```

Pay attention, now, as we want to use the normalized data, in the partitioning part we need to use the `normalizedData` variable instead of `data`.

```st
subsets := partitioner split: normalizedData withProportions: #(0.75 0.25).
```

## Testing the model

Now we can make predictions for previously unseen values: check if a breast cancer is maligne or benigne based on its parameters. To make a prediction we need to send the message `predict:` to the SVM model with the data that we want to predict as an argument.

```st
yPredicted := AISupportVectorMachines predict: xTest.
```
