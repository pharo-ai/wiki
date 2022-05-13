# K-Nearest Neighbors

Repository: https://github.com/pharo-ai/k-nearest-neighbors

K-nearest neighbors is a supervised, non parametric, classifier. It can be used for both regression and classification, but it is more generally used for classification problems.

For classifying new data it calculates the distance of the point to the k-nearest neighbors and classifies the point with the class label of the majority.

## Using it

```st
kNN := AIKNearestNeighbors new.

kNN fitX: inputMatrix y: outputVector.
kNN predict: aCollectionOfPoints
```

## K-Nearest-Neighbors Distance metrics

For calculating the distance between two points, k-nearest-neoghbors uses by defult the euclidean distance. But one can also specify another distances for the calculations.

For using other distance metric, one can send the following messsages:

```st
kNN := AIKNearestNeighbors k: 5.

kNN useEuclideanDistance.
kNN useHammingDistance.
kNN useManhattanDistance.

kNN useMinkowskiDistanceWithPValue: 4
```

### Euclidean Distance

This is the distance that is used by default. It uses the euclidean formula to calculate the distance. One limitation is that this metric will work properly only for real valued vectors.

```
d(x, y) = ( Σ (yi - xi)^2 ) sqrt
```

### Manhattan distance

```
d(x, y) = Σ abs (yi - xi)
```

### Minkowski Distance

This distance needs to be initialized with a p value.

```
kNN useMinkowskiDistanceWithPValue: 4
```

The formula is:

```
d(x, y) = ( Σ abs (yi - xi) ) ^ (1/p)
```

### Hamming Distance

The Hamming distance is mostly used for binary vectors of string vector. It counts the points in which the vectors do not match.