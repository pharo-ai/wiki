# K-Means Clustering Algorithm

K-Means is an unsupervised machine learning algorithm that clusters the data into k-clusters. The algorithm consists of 3 basic steps:

1. Initialize the initial centroids
2. Assign all the points to the closest centroid using the Euclidean distance
3. Update the centroids using the mean of all its points
4. Repeat steps 2 and 3 until all the centrois remain the same

After some time the algorithm will converge, but it can be to a local minimum. This depends on the initialization of the centroids. For bypassing this problem, the algorithm is ran several times and it will return the best centroids that were found. See `AIKMeans class>>#defaultNumberOfTimesItIsRun`.

There are different strategies for initializing the centroids. For the moment in this implementation, we choose k-random points that are in the range of the data. That means, we take the max axis for each of the coordinates (in a 2 dimensional mathematical space we take `x` and `y`), and we chose a random point between those limits.

## Using K-Means

For creating an instance of the object, you need to specify the numbers of clusters. For that you can use the constructor method `numberOfClusters:`

```st
kMeans := AIKMeans numberOfClusters: 3.
```

With the method `fit:` you can train the model. As K-Means is a clustering algorithm, that means unsupervised learning, we only need to pass the dependent variable to train the model.

```st
kMeans fit: aCollectionOfPoints.
```

After the model is fitted, you can access to the clusters using the `clusters` accessing method.
The final clusters are the result of the clustering of the data.

```st
kMeans clusters
```

For clustering unseen data you can use the `predict:` method. It will return the clustering for the new data (data that was not used for training the model).

```st
kMeans predict: aCollection
```

For getting the score of the K-Means model you can send the message `score:`. The score in this case is the sum of the Euclidean distance between all the points and their centroids. It is also called inertia.

```st
kMeans score: aCollectionOfPoints
```
