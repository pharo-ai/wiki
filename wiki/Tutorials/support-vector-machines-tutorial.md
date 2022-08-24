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
file := '/Users/oleks/Downloads/data.csv' asFileReference.
data := DataFrame readFromCsv: file.
```

```st
data columnNames. "an OrderedCollection('id' 'diagnosis' 'radius_mean' 'texture_mean' 'perimeter_mean' 'area_mean' 'smoothness_mean' 'compactness_mean' 'concavity_mean' 'concave points_mean' 'symmetry_mean' 'fractal_dimension_mean' 'radius_se' 'texture_se' 'perimeter_se' 'area_se' 'smoothness_se' 'compactness_se' 'concavity_se' 'concave points_se' 'symmetry_se' 'fractal_dimension_se' 'radius_worst' 'texture_worst' 'perimeter_worst' 'area_worst' 'smoothness_worst' 'compactness_worst' 'concavity_worst' 'concave points_worst' 'symmetry_worst' 'fractal_dimension_worst' nil)"
```

```st
"clean data"
data removeColumns: #('id' nil).

diagnosisMapping := { 'M' -> 1 . 'B' -> 0 } asDictionary.

data column: 'diagnosis' transform: [ :column |
	column collect: [ :each | diagnosisMapping at: each ] ].

normalizedData := data normalized.
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
xTrain := trainData columns: (data columnNames copyWithout: 'diagnosis').
yTrain := trainData column: 'diagnosis'.

xTest := testData columns: (data columnNames copyWithout: 'diagnosis').
yTest := testData column: 'diagnosis'.
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

```st
"Adding 1 to the beginning of each row"
xTrain := xTrain collect: [ :row | row copyWith: 1 ].
xTest := xTest collect: [ :row | row copyWith: 1 ].
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

## Testing the model

Now we can make predictions for previously unseen values: check if a breast cancer is maligne or benigne based on its parameters. To make a prediction we need to send the message `predict:` to the SVM model with the data that we want to predict as an argument.

```st
yPredicted := model predict: xTest.
```
