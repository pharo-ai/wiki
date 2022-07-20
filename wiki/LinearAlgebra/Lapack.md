# Pharo LAPACK

Repository: https://github.com/pharo-ai/lapack

A minimal FFI binding for LAPACK (http://www.netlib.org/lapack) in Pharo.

For creating another binding to another method (or routine, because the library is written in Fortran), you only need to create a subclass of `LapackAlgorithm`. You can check `LapackDgelsd` as an example. 

_Note: We only tested this for MacOS. But it should work on Windows and Linux too. A prerequisite is to have already intalled the library on your OS. For making it work on Linux and Windows, is only needed to override the methods `unixLibraryName` and `win32LibraryName` to return the path in which the library is installed on your system. Check `LapackLibrary>>#macLibraryName`_

## How to use it?

Currently, we only bound one routine: `dgelsd()` in the class `LapackDgelsd`

This algorithm computes the minimum-norm solution to a linear least squares problem double precision GE matrices. If you don't know about lapack nor about the least squares problem, please see the class comment (`LapackDgelsd`). For now, we are going to continue the explanation assuming that the reader knows about it.

To use the method, you need to pass two matrices: `matrixA` and `matrixB`. The object needs to understand the messages `contentsForLapack` and `contentsForLapackOfAtLeast:`. Those methods are implemented in `Collection` as extensions. So, you can pass an Array or a Collection.

Be careful, this is the binding for the fortran lapack and it expects that the matrices are flattened in a column-major form. If you want to use this library in a high-level way, we strongly recomend using the [linear-algebra](https://github.com/pharo-ai/linear-algebra) library of pharo-ai. It uses this lapack binding for solving the least square algorithm and it provides a nicer API and also has the matrices data structures that are internally store the elements already flattened in a column-major way and that you can use directly.

### Code example

For example, you can use the `LapackDgelsd` algorithm in the following way:

```st
matrixA := #( 0.12 -6.91 -3.33  3.97 -8.19
              2.22 -8.94  3.33  7.69 -5.12
             -6.72 -2.74 -2.26 -9.08 -4.40
             -7.92 -4.71  9.96 -9.98 -3.20 ).
matrixB := #( 7.30  1.33  2.68 -9.62 0.00
              0.47  6.58 -1.71 -0.79 0.00
             -6.28 -3.42  3.46  0.41 0.00 ).

algorithm := LapackDgelsd new
    numberOfRows: 4;
    numberOfColumns: 5;
    numberOfRightHandSides: 3;
    matrixA: matrixA;
    matrixB: matrixB;
    yourself.

algorithm solve.

"To get the result, we have to call the accessors.
Info represents if the process completed with success"
algorithm info.

"the array with the solutions"
algorithm minimumNormSolution.

"The effective rank"
algorithm rank.

"And the singular values"
algorithm singularValues.
```
