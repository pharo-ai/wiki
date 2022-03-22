# Clustering Users of a Credit Card Company using the K-Means Algorithm

In this tutorial we will use the k-means clustering algorithm for clustering the clients based on their credit card consumption.

### Data Analysis and Manipulation

First, we will load the dataset.

```st
creditCardData := AIDatasets loadCreditCard.
```

If we inspect (open Pharo Inspector) the DataFrame, we can see that there is 18 features for each client of the credit card company.

<img src="./img/credit-card-dataset-inspector.png" height="450"/>

Also, after inspecting the data, we can see that it contains nil fields, so we will replace them with zeros

```st
creditCardData replaceAllNilsWithZeros.
```

Currently, the ID parameter is a string. It is the letter C along with a number. For example: `C10001`
So, we convert the ID field to be a numeric field

```st
creditCardData
    toColumn: 'CUST_ID'
    applyElementwise: [ :element | element asInteger ].
```

### K-Means Algorithm

For now, the K-Means algorithm expects an `Array` or a `Collection`. The data is an object of `DataFrame`. We need to convert the data from DataFrame to Array.

[Optional]
To speed up the computation, we will take 1000 random elements of the data. You can run it with the whole dataset but it will take some minutes to execute.

```st
dataAsArray := creditCardData asArrayOfRows shuffled first: 1000.
```

The data is too big and it has too many dimensions to be visualised. So, to find the best number of clusters, we will train the model with 12 different cluster numbers.
Then, we will plot the error of the model with each number of clusters.

```st
numberOfClustersCollection := 2 to: 12.

"Create a collection for storing the errors."
inertias := OrderedCollection new.

"We train 11 times k-means models, and store the error of each of them"
numberOfClustersCollection do: [ :numberOfClusters |
	kMeans := AIKMeans numberOfClusters: numberOfClusters.
	kMeans fit: dataAsArray.  
	inertias add: (kMeans score: dataAsArray) ].
```

By definition, if the number of clusters increase the error will always reduce. There is different technoques to find the best number of clusters. We will use the elbow method.

If we plot the errors, the curve will look like an arm. We need to manually the point in which the plot starts decreasing the looses.

We will use [Roassal 3](https://github.com/ObjectProfile/Roassal3) for doing the plot. We will not explain the code in this tutorial.

According to the elbow plot, we can see that the best number of cluster is nine clusters.

<img src="./img/elbow-method-credit-card.png" height="450"/>

Code for plotting the elbow plot.

```st
"Elbow draw with Roassal"
elbowChart := RSChart new.
elbowChart extent: (numberOfClustersCollection size * 20) @ (7 * 20).
elbowChart addPlot:
	(RSLinePlot new
		x: numberOfClustersCollection y: inertias;
		color: Color red;
		fmt: 'o';
		yourself).
elbowChart addDecoration:
	(RSHorizontalTick new
		numberOfTicks: numberOfClustersCollection size;
		integer;
		yourself).
elbowChart addDecoration:
	(RSVerticalTick new
		asFloat;
		yourself).

elbowChart xlabel: 'Number of Clusters'.
elbowChart ylabel: 'Inertia'.
elbowChart title: 'Elbow '.
elbowChart build.
elbowChart canvas open.
```

If we look at the elbow plot, we it seems that the best number of clusters is 8. Note that you may have not the exact same results. 

We train the algorithm again with that number of clusters.

```st
kMeans := AIKMeans numberOfClusters: 8.
kMeans fit: dataAsArray.  

clusters := kMeans clusters.
```

## All the code

```st
""
"DATA ANALYSIS AND MANIPULATION"
""

"Load credit card dataset"
creditCardData := AIDatasets loadCreditCard.

"If we inspect the DataFrame, we can see that there is 18 features for each client of the credit card company"

creditCardData columnNames. "('CUST_ID' 'BALANCE' 'BALANCE_FREQUENCY' 'PURCHASES' 'ONEOFF_PURCHASES' ...)"

"Also, after inspecting the data, we can see that it contains nil fields, 
so we will replace them with zeros"
creditCardData replaceAllNilsWithZeros.

"Currently, the ID parameter is a string.
It is the letter C along with a number. For example: C10001
So, we convert the ID field to be a numeric field"
creditCardData
    toColumn: 'CUST_ID'
    applyElementwise: [ :element | element asInteger ].

""
"K-MEANS ALGORITHM CLUSTERING"
""

"Convert the data from DataFrame to Array and take 1000 random elements to speed up."
dataAsArray := creditCardData asArrayOfRows shuffled first: 1000.

"We train the model with 12 clusters"
numberOfClustersCollection := 2 to: 12.

"Create a collection for storing the errors."
inertias := OrderedCollection new.

"We train 11 k-means models, and store the error of each of them"
numberOfClustersCollection do: [ :numberOfClusters |
	kMeans := AIKMeans numberOfClusters: numberOfClusters.
	kMeans fit: dataAsArray.  
	inertias add: (kMeans score: dataAsArray) ].

"If we look at the elbow plot, we see that 8 clusters seems to be the best solution."
kMeans := AIKMeans numberOfClusters: 8.
kMeans fit: dataAsArray.  

clusters := kMeans clusters.
```

## Principal Component Analysis

Principal component analysis (PCA), is a statistical method that allows to summarize the information of a dataset. It gives the k-most representative features of data. It can be used to more easily visualise the data.

For doing this, we will use [PolyMath](https://github.com/PolyMathOrg/PolyMath) library that is loaded in the pahro-ai library.

[Optional]
For speeding purposes, we will take 1000 random elements of the data. You can run it with the whole dataset but it will take some minutes to execute.

```st
"For doing the principal component analysis (PCA), we will take randomly 1000 elements for speed up the computation"
shuffledData := creditCardData asArrayOfRows shuffled first: 1000.
```

Now, we create our PolyMath matrix

```st
polyMathMatrix := PMMatrix rows: shuffledData.
```

Wa want to plot the data. So, we want 2 parameters, one for the x axis and the other one for the y axis.

We train the principal component analyser for 2 components.

```st
pca := PMPrincipalComponentAnalyserSVD new.
pca componentsNumber: 2.
pca fit: polyMathMatrix.
principalComponents := pca transform: polyMathMatrix.

firstPrincipalComponent := principalComponents rows collect: [ :each | each first].
secondPrincipalComponent := principalComponents rows collect: [ :each | each second].
```

As we discussed in the last part, we see that 8 is the number of clusters that work the best. We train again our model.

```st
kMeans := AIKMeans numberOfClusters: 8.
kMeans fit: shuffledData.  

clusters := kMeans clusters.
```

We use those `x` and `y` to plot the data with its different groups.

```st
colors := RSColorPalette qualitative dark28 range.

clusteredDataChart :=RSChart new.
clusteredDataChart addPlot: (plot := RSScatterPlot new x: firstPrincipalComponent y: secondPrincipalComponent ).
clusteredDataChart 
    xlabel: 'most representative feature';
    ylabel: 'second most representative feature';
    title: 'Clustered data reduced to 2 dimensions'.

clusteredDataChart build.

plot ellipses doWithIndex: [ :e :i| 
    e color: (colors at: (clusters at: i)) ].

clusteredDataChart canvas open.
```

We have to keep in mind that the data has 18 dimensions, but we plot it only using 2. So, we lost information in terms of visualisation. Also, we choose the principal components using information of around only 1/9 of the whole dataset. Finallly, it can be that the k-means algorithm may not be the best approach for this problem.

<img src="./img/credit-card-reduced-two-dimensions.png" height="450"/>

## All the dimensionality reduction code

```st
"take randomly 1000 elements for speed up the computation"
shuffledData := creditCardData asArrayOfRows shuffled first: 1000.

polyMathMatrix := PMMatrix rows: shuffledData.

pca := PMPrincipalComponentAnalyserSVD new.
pca componentsNumber: 2.
pca fit: polyMathMatrix.
principalComponents := pca transform: polyMathMatrix.


"If we look at the elbow plot, we see that 8 clusters seems to be the best solution."
kMeans := AIKMeans numberOfClusters: 8.
kMeans fit: shuffledData.  

clusters := kMeans clusters.

"Get the principal components"
firstPrincipalComponent := principalComponents rows collect: [ :each | each first].
secondPrincipalComponent := principalComponents rows collect: [ :each | each second].

"Visualisation to show the different groups that were found using the clustering algorithm"

colors := RSColorPalette qualitative dark28 range.

clusteredDataChart :=RSChart new.
clusteredDataChart addPlot: (plot := RSScatterPlot new x: firstPrincipalComponent y: secondPrincipalComponent ).
clusteredDataChart 
    xlabel: 'most representative feature';
    ylabel: 'second most representative feature';
    title: 'Clustered data reduced to 2 dimensions'.

clusteredDataChart build.

plot ellipses doWithIndex: [ :e :i| 
    e color: (colors at: (clusters at: i)) ].

clusteredDataChart canvas open.
```
