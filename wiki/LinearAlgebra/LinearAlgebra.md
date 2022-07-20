# Linear Algebra

Repository: https://github.com/pharo-ai/linear-algebra

Fast linear algebra implemented using [Pharo Lapack](https://github.com/pharo-ai/lapack).

## Matrices implemented

We have implemented several matrices for usign them as data structures.

All the matrices are subclasses of `AIAbstractMatrix`. They have the same API for creating the matrices.

You can create a matrix with no elements:

```st
AIAbstractMatrix newRows: aNumberOfRows columns: aNumberOfColumns
```

For creating a matrix with the rows represented as an array of arrays

```st
AIAbstractMatrix rows: collectionOfRows
```

### Contiguous matrix `AIContiguousMatrix`

The abstract class `AIContiguousMatrix` stores the elements in a flat array. That means we can access one element of the matrix on constant time. 

We have implemented 3 types of matrices:

- `AIColumnMajorMatrix`
- `AIRowMajorMatrix`

The difference is how the store the elements in the flat array. Column mejor or row major.

- `AINativeFloatMatrix`

This matrix also stores the elements in a column major form. But, for it uses a `Float64Array` for storing them. This can be very useful when we want to use this matrix with foreigner functions. Because, when calling, for example C, from Pharo we need to convert the Pharo object into C object. This can take time. So, one can use this matrix for speeding up as we do not need to do the convertion. By the other hand, if one wants to use this matrix in Pharo is not a good idea since it will convert each object into a Pharo object and then into a native object again.

## Implemented algorithms

- For the moment, we only have implemented a solver for the [Least Squares Problem](https://en.wikipedia.org/wiki/Least_squares)	

For using it, first you need to use the `AIColumnMajorMatrix` as this solver uses Fortran Lapack that expects to be flattened in a column major way.

```st
matrixA := #(
    ( 0.12  -8.19   7.69  -2.26  -4.71)
    (-6.91   2.22  -5.12  -9.08   9.96)
    (-3.33  -8.94  -6.72  -4.40  -9.98)
    ( 3.97   3.33  -2.74  -7.92  -3.20)) asAIColumnMajorMatrix.
	
matrixB := #(
    (7.30   0.47  -6.28)
    (1.33   6.58  -3.42)
    (2.68  -1.71   3.46)
    (-9.62  -0.79   0.41)) asAIColumnMajorMatrix.	
	
algo := AILeastSquares new
    matrixA: matrixA;
    matrixB: matrixB;
    yourself.
	
algo solve.

algo solution. "AIColumnMajorMatrix(
    (-0.69  -0.80  0.38   0.29   0.29)
    (-0.29  -0.48  0.51   0.20   0.18)
    (-0.02  -0.15  0.26  -0.18   0.04) )"
	
algo singularValues. "#(18.66 15.99 10.01 8.51)"
algo rank. "4"
```
