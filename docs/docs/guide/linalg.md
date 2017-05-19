# Matrices

Koma's `Matrix<T>` type is a 2D container designed to be used for linear algebra.

### Creating Matrices

While it is always possible to use the `*Factory` classes provided by your chosen backend directly,
Koma provides a set of top-level convenience functions to easily create new matrices. For example:

```kotlin
// x is a 3x3 identity matrix
var x = eye(3)

// x is a 3x3 with Gaussian random noise, distribution 0,1
x = randn(3,3)

// x is a 3x3 with uniform random noise
x = rand(3,3)

// x is a 4x5 matrix where the ith row and jth column has the value i+j*2
x = fill(4,5) { row, col -> row+col*2 }

val someData = doubleArrayOf(doubleArrayOf(1,2,3),
                             doubleArrayOf(4,5,6))
// x is a 2x3 matrix constructed from the data in a java array of arrays
x = create(someData)
```

See [here](https://github.com/kyonifer/koma/blob/master/core/src/koma/creators.kt) for a complete list of
available creators. There is also a DSL for matrix literals:

```kotlin
// a is a 2x3 matrix filled with the numbers passed in
val a = mat[1,2,3.3 end
            4,5,6]
```

### Math Functions
Matrices have useful map functions that return matrices for chaining operations (see [here](core/src/koma/extensions.kt) for a complete list).

```kotlin
    // Create a 3x3 identity and then add 0.1 to all elements
    val x = eye(3) + 0.1

    // Map each element through a function that adds .01
    val y = x.mapMat { it + .01 }
    
    // Map each element through a function that adds or subtracts depending on the element index
    val z = x.mapMatIndexed { row, col, ele -> if (row > col) ele + 1 else ele - 1 }

    // Are there any elements greater than 1?
    val hasGreater = x.any { it > 1 }

    // Are all elements greater than 1?
    val allGreater = x.all { it > 1 }

    // Apply a function to a row at a time and store the outputs in a contiguous matrix
    val sins = x.mapRows { row -> sin(row) }
    
    // Print all elements greater than 1
    x.each { if (it>1) println(it) }
    
``` 

We can also do some linear algebra:

```kotlin

    // Matrix literal syntax, see creators.kt for 
    // convenience functions like zeros(5,5)
    var A = mat[1,0,0 end
                0,3,0 end
                0,0,4]

    // Calculate the matrix inverse
    var Ainv = A.inv()

    var b = mat[2,2,4].T

    // Use overloaded operators:
    
    // * is matrix multiplication 
    var c = A*b + 1
    
    // emul is element-wise multiplication
    var d = (A emul A) + 1

    // Number of decimals to show
    format("short")

    println(c)

```

Which produces:

```
Output:

mat[ 3.00  end
     7.00  end
     17.00 ]
```

Many special functions are supported (see [the matrix interface](core/src/koma/matrix/Matrix.kt) for a complete list):

```kotlin

    val a = 2*eye(3)+.01 // eye is identity matrix
    
    a.chol()  // Cholesky decomposition
    a.det()   // Determinant
    a.diag()  // Diagonal vector
    a.inv()   // Matrix inverse
    a.norm()  // Matrix norm

```

Scalar functions can be applied elementwise to matrices (see [here](core/src/koma/scalarfuncs.kt) for a complete list):

```kotlin
    val x = create(0..100)/5.0  // Matrix of 0, 1/5, 2/5, ...
    val y = sin(x)              // Sin applied elementwise
    plot(y)                     // Plot of sin function
```

Matrix indexing and slicing is supported (see [here](core/src/koma/operators.kt) for a list of operators as well as the Matrix<T> type):

```kotlin

    val x = randn(5,5)
    val y = x[0,0..4] // Grab the first row
    
    x[0..2,0..3] = zeros(3,4) // Set the upper-left 3x4 sub-matrix of x to zero

```

A Matrix is convertible to an Iterable:

```kotlin
    val x = randn(5,5).toIterable()
    
    // Adds all elements and returns sum
    x.reduce { x, y -> x+y }
    
    // Returns list of all elements greater than 4
    x.find { it > 4 }

```
