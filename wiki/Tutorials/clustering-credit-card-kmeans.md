# Clustering Users of a Credit Card Company using the K-Means Algorithm

### Data Analysis and Manipulation

First, we will load the dataset.

```st
creditCardData := AIDatasets loadCreditCard.
```

If we inspect (open Pharo Inspector) the DataFrame, we can see that there is 18 features for each client of the credit card company.

```st
creditCardData columnNames. "('CUST_ID' 'BALANCE' 'BALANCE_FREQUENCY' 'PURCHASES' 'ONEOFF_PURCHASES'
'INSTALLMENTS_PURCHASES' 'CASH_ADVANCE' 'PURCHASES_FREQUENCY' 'ONEOFF_PURCHASES_FREQUENCY'
'PURCHASES_INSTALLMENTS_FREQUENCY' 'CASH_ADVANCE_FREQUENCY' 'CASH_ADVANCE_TRX' 'PURCHASES_TRX'
'CREDIT_LIMIT' 'PAYMENTS' 'MINIMUM_PAYMENTS' 'PRC_FULL_PAYMENT' 'TENURE')"
```

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

For now, the K-Means algorithm expects an `Array` or a `Collection`. The data is an object of `DataFrame`. We need to convert the data from DataFrame to Array

```st
dataAsArray := creditCardData asArrayOfRows.
```

The data is too big and it has too many dimensions to be graph. So, to find the best number of clusters, we will train the model with 12 clusters.
Then, we will graph the error of the model with each number of clusters.

```st
numberOfClustersCollection := 2 to: 12.

"Create a collection for storing the errors."
inertias := OrderedCollection new.

"We train 11 k-means models, and store the error of each of them"
numberOfClustersCollection do: [ :numberOfClusters |
	kMeans := AIKMeans numberOfClusters: numberOfClusters.
	kMeans fit: dataAsArray.  
	inertias add: (kMeans score: dataAsArray) ].
```

By definition, if the number of clusters the error will always reduce. There is different technoques to find the optimal number of clusters. We will use the elbow method.

If we graph the errors, the curve will look like an arm. We need to manually the point in which the graph starts decreasing the looses.

We will use [Roassal 3](https://github.com/ObjectProfile/Roassal3) for doing the plot. We will not explain the code in this tutorial.

According to the elbow graph, we can see that the optimal number of cluster is nine clusters.

![](./img/elbow-method-credit-card.png)

Code for plotting the elbow graph.

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

If we look at the elbow graph, we see that the best number of clusters is 9. We train the algorithm again with that number of clusters 
```
kMeans := AIKMeans numberOfClusters: 9.
kMeans fit: dataAsArray.  

clusters := kMeans clusters.
```

Summary of the code of the whole tutorial

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

"For now, the K-Means algorithm expects an Array or a Collection. The data is an object of DataFrame. We need to convert the data from DataFrame to Array"
dataAsArray := creditCardData asArrayOfRows.

"We train the model with 12 clusters to find the best one"
numberOfClustersCollection := 2 to: 12.

"Create a collection for storing the errors."
inertias := OrderedCollection new.

"We train 11 k-means models, and store the error of each of them"
numberOfClustersCollection do: [ :numberOfClusters |
	kMeans := AIKMeans numberOfClusters: numberOfClusters.
	kMeans fit: dataAsArray.  
	inertias add: (kMeans score: dataAsArray) ].

"If we look at the elbow graph, we see that the best number of clusters is 9. We train the algorithm again with that number of clusters"

kMeans := AIKMeans numberOfClusters: 9.
kMeans fit: dataAsArray.  

clusters := kMeans clusters.
```

## Principal Component Analysis

```st
"For doing the principal component analysis (PCA), we will take randomly 1000 elements for speed up the computation"
shuffledData := creditCardData asArrayOfRows shuffled first: 1000.

polyMathMatrix := PMMatrix rows: shuffledData.

pca := PMPrincipalComponentAnalyserSVD new.
pca componentsNumber: 2.
pca fit: polyMathMatrix.
principalComponents := pca transform: polyMathMatrix.


"If we look at the elbow graph, we see that the best number of clusters is 8.
So, we train the algorithm again with that number of clusters"

kMeans := AIKMeans numberOfClusters: 8.
kMeans fit: shuffledData.  

clusters := kMeans clusters.

"Graph to show the different groups that were found using the clustering algorithm"
x := principalComponents rows collect: [ :each | each first].
y := principalComponents rows collect: [ :each | each second].

colors := RSColorPalette qualitative dark28 range.

clusteredDataChart :=RSChart new.
clusteredDataChart addPlot: (plot := RSScatterPlot new x: x y: y ).
clusteredDataChart 
    xlabel: 'X Axis';
    ylabel: 'Y Axis';
    title: 'Clustered data reduced to 2 dimensions'.

clusteredDataChart build.

plot ellipses doWithIndex: [ :e :i| 
    e color: (colors at: (clusters at: i)) ].

clusteredDataChart canvas open.
```