# K-Means Clustering Algorithm

K-Means is an unsupervised machine learning algorithm that clusters the data into k-clusters. The algorithm consists of 3 basic steps:

1. Initialize the initial centroids
2. Assign all the points to the closest centroid using the Euclidean distance
3. Update the centroids using the mean of all its points
4. Repeat steps 2 and 3 until all the centrois remain the same

After some time the algorithm will converge, but it can be to a local minimum. This depends on the initialization of the centroids. For bypassing this problem, the algorithm is ran several times and it will return the best centroids that were found. See `AIKMeans class>>#defaultNumberOfTimesItIsRun`.

There are different strategies for initializing the centroids. For the moment in this implementation, we choose k-random points that are in the range of the data. That means, we take the max axis for each of the coordinates (in a 2 dimensional mathematical space we take `x` and `y`), and we chose a random point between those limits.

## K-Means++ centroids initialization algorithm

K-Means pharo-ai implementation uses by default the k-means++ centroids initialization algorithm.

Using the algorithm instead of randomly choosing the initial centroids, increacces the speed in the centroids initialization step, but, makes the algorithm to converge faster.

It was proposed in 2007 by Arthur et Vassilvitskii.

The algorithm is as follows:

```
1. Choose the first centroid to be a random point.
2. Calculate the distance of all the point to the choosen clusters. Keep the min distance of a point to the choosen clusters.
3. Choose the next cluster the point being the farest being the one with the most probability of being choose.
4. Repeat Steps 2 and 3 k centroids are selected
```

## Using K-Means

## Setting the parameters

For creating an instance of the object, you need to specify the numbers of clusters. For that you can use the constructor method `numberOfClusters:`

```st
kMeans := AIKMeans numberOfClusters: 3.
```

You also can configure the max iterations, the numbers of clusters and the number of time the the k-means algorithm will be run.

```st
kMeans
    maxIterations: 1000;
    numberOfClusters: 4;
    timesToRun: 4
```

## Fitting
]
With the method `fit:` you can train the model. As K-Means is a clustering algorithm, that means unsupervised learning, we only need to pass the dependent variable to train the model.

```st
kMeans fit: aCollectionOfPoints.
```

## Clustering


After the model is fitted, you can access to the clusters using the `clusters` accessing method.
The final clusters are the result of the clustering of the data.

```st
kMeans clusters
```

For clustering unseen data you can use the `predict:` method. It will return the clustering for the new data (data that was not used for training the model).

```st
kMeans predict: aCollection
```

## Evaluating

For getting the score of the K-Means model you can send the message `score:`. The score in this case is the sum of the Euclidean distance between all the points and their centroids. It is also called inertia.

```st
kMeans score: aCollectionOfPoints
```

The transform method will calculate the euclidean distance from each of the points, that are passed as arguments, to their centroids.

```st
kMeans transform: aCollectionOfPoints
```