# The Stamp Language Specification

<b>Disclaimer: The langauge is currently in development and is subject to minor changes.</b>

Stamp is designed with minimal structures and overal boilerplate, with a specific yet powerful implentation of assignment and control flow - stamping.

## File format
Stamp programs are [UTF-8](https://en.wikipedia.org/wiki/UTF-16) files with the ```.stamp``` file extension. It's just plain text.

## Comments
All characters following a ```//``` are comments, just like the C family. There are no multiline comments.

``` c
// this is a comment
```

## Types
Dynamically typed, Stamp supports the following types:
- signed long integers (32-bit)
- floats (32-bit, [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754))
- chars (max 32-bit, [UTF-8](https://en.wikipedia.org/wiki/UTF-16))
- arrays, denoted as ```[ .. , .. , .. ]```
- strings, denoted as ```" ... "``` (implicitly arrays of chars)

Integers, floats and arrays are converted to their UTF-8 representation before being output, however chars are output as the corresponding UTF-8 value. Since strings are actually arrays with chars, they must be output as such (using a for loop).

Booleans are functional through truthiness. ```0``` for both integers and floats, the empty string (```""```) and empty array (```[]```) are all false. All other values are true. This is how control flow is determined. False and true are represented by ```0``` and ```1``` respectively.

<!-- All types are implicitly arrays. For example, ```5``` is actually ```{5}```. Further, arrays can hold heterogeneous types, ie. ```{5, 3.14, "hello", true, {7, 2}}``` is valid. -->

Note that arrays are not ordinal so performing greater than or less than comparison will thrown a [runtime error](#runtime-error).

### Length
For arrays, the length is the number of elements. For all other types, the length is one.

|             Example             |   Type  | Length |
|:-------------------------------:|:-------:|:------:|
|             ```2```             | integer |    1   |
|            ```3.14```           |  float  |    1   |
|            ```'a'```            |   char  |    1   |
|           ```"Hi!"```           |  string |    3   |
| ```[2, 3.14, 'a', "Hi!", []]``` |  array  |    5   |

<!-- ``` c
                                    //  TYPE     LENGTH
two == 2                            // integer     1
pi == 3.14                          //  float      1
a == "a"                            //  char       1
hi == "Hi!"                         // string      3     (implicitly ["H", "i", "!"])
yes == 1                            // integer     1
all == [two, pi, a, hi, yes, []]    //  array      6
``` -->

## Stamping
The stamp is a powerful operator that can be used for assignment and can assist with control flow. A stamp consists of both a head (```=```, ```<```, ```>``` and ```!```) and a handle (```=``` and ``` ```). The head indicates how the truthiness of the stamp is determined and the handle indicates whether or not the assignment is executed.

Below is an explaination of all eight possible stamps with an example.

|    Example   | Truthiness predicate | New ```x``` value |
|:------------:|:--------------------:|:-----------------:|
| ```x == 5``` |    x is equal to 5   |         5         |
| ```x <= 5``` |   x is less than 5   |         5         |
| ```x >= 5``` |  x is greater than 5 |         5         |
| ```x != 5``` |      x is not 5      |         5         |
|  ```x = 5``` |    x is equal to 5   |        N/A        |
|  ```x < 5``` |   x is less than 5   |        N/A        |
|  ```x > 5``` |  x is greater than 5 |        N/A        |
|  ```x ! 5``` |      x is not 5      |        N/A        |



<!-- ``` c
            //    RETURN TRUTHINESS       NEW VALUE FOR x
x == 5      //       x equals 5                 5
x <= 5      //     x less than 5                5
x >= 5      //    x greater than 5              5
x != 5      //     x not equals 5               5

x = 5       //       x equals 5             no change
x < 5       //     x less than 5            no change
x > 5       //    x greater than 5          no change
x ! 5       //     x not equals 5           no change
``` -->

The truthiness of the predicate is returned by the stamping.

The left side of the stamp is the target variable and the right side is the source expression. For the above examples, ```x``` is the target variable and ```5``` is the source expression.

If the left side of the stamp is not a variable, a [syntax error](#syntax-error) will be thrown.

Note that in Stamp, ```=``` is used for equality and ```==``` is used for assignment (and equality). This differs from convention.

(It can assign part or all of a variable onto another variable and returns the truthiness of the assignment)


### Assignment
There are two types of assignment: direct and indirect. Direct assignment occurs when the target variable is not initialised or when the two sides have equal [length](#length). Indirect assignment occurs when the target variable has a length less than the source expression.

If the target variable has a length longer than the source expression a [runtime error](#runtime-error) will be thrown.

**[ INSERT TABLE FOR WHAT TYPE OF ASSIGNMENT IS USED WHEN ]**

<!-- |                                    |      Left stamp     |     Right stamp     |   Ink stamp   |      Dual stamp     |
|------------------------------------|:-------------------:|:-------------------:|:-------------:|:-------------------:|
| Target expression contains literal |     syntax error    |     syntax error    |      N/A      |     syntax error    |
| Uninitialised source expression    | direct (deallocate) | direct (deallocate) | runtime error | direct (deallocate) |
| Same length                        |        direct       |        direct       |      none     |        direct       |
| Different length                   |       indirect      |       indirect      |      none     |       indirect      | -->

#### Direct assignment
If the target variable is not initialised or the target variable and the source expression have the same length, then stamping works the same way as assignment in other languages.

For example, this assigns ```5``` to ```x``` and returns false (0) since it is not initialised:
``` c
x == 5
```
And this assigns ```{3, 4}``` to ```x``` and returns true (1) since ```[1, 2]``` is not equal to ```[3, 4]```:
``` c
// initialise x and y
x == [1, 2]
y == [3, 4]

// assign y to x
x != y
```

#### Indirect assignment

### Control flow
If the target variable has a length less than the source expression, then...

<!-- The stamp also returns whether the target variable and source expression were equal. This is often ignored, but can be used for program flow. -->

## Structures
Also taken from the C family, ```{ ... }``` are used to wrap a body of code, called a well. This well is execute if the immediately preceding statement (often a stamp) is true and will continue to run until the statement is no longer true.

The following subsections demonstrate examples of Stamp code.

### While
If ```x``` equals ```5```, then the statment is true. While the statement is true, the well will continue to execute.
``` c
x == 5 {
    ...
}
```

### For
In Stamp, a for loop and a for each loop are equivalent.
``` c
x == [1, 2, 3] {
    ...
    // runs three times
}
```
Another example:
``` c
x == [2, 3]
x == [1, 2, 3] {
    ...
    // runs twice
}
```

**[ EXPLAIN HOW ```io```/```terminal``` WORKS ]**

The program,
``` c
x == [1, 2, 3] {
    io == x
}
```
outputs
```
123
```

The program,
``` c
x == [2, 3]
x == [1, 2, 3] {
    io == x
}
```
outputs
```
[1, 2][2, 3]
```

### Conditional statements
This section include the family of if statements.

#### If

``` c
x = 5 {
    ...
}
```
<!-- If ```x``` equals ```5```, then we want the well to execute, however, that would be a while loop. To emmulate an if statement, we only run the first iteration of a while loop. To do this, we use a temporary variable that we change during the first iteration. -->

<!-- ``` c
x_ == x
x_ = 5 {
    ...
    x_ == x_ + 1
}
``` -->

If not
``` c
x ! 5 {
    ...
}
```
<!-- ``` c
x_ == x
x_ ! 5 {
    ...
    x_ == x_ + 1
}
``` -->

#### If else
``` c
x_ == x
// if
x = 5 {
    ...
}
// else
x ! 5 {
    ...
}
```
or better,
``` c
x_ == x
x__ == x
// if
x = 5 {
    ...
    x_ == x_ + 1
}
// else
x_ = x__ {
    ...
}
```
**[ WRITE EXPLANATION ]**
<!-- Again, we use a temporary variable to only execute the first iteration of the while loop. However, observe that if the 'if' well is executed, then the temporary variable will be changed. This will cause the 'else' clause to be false. If the 'if' will is not executed, then the temporary variable remains unchanged. To test is the temporary variable is changed, we can use a second temporary constant, set to the initial value of ```x```.

Note that if ```x``` does not change in the 'if' well, then the temporary constant is not needed and you can use ```x``` instead of the temporary constant in the 'else' clause.
``` c
x_ == x
x__ == x
// if
x_ = 5 {
    ...
    x_ == x_ + 1
}
// else
x_ = x__ {
    ...
}
``` -->

#### If elif else
**[ WRITE THIS ]**


### Do while
``` c
repeat == 1
repeat = 1 {
    ...
    fail_condition {
        repeat == 0
    }
}
```

<!-- ### If while
``` c
x_ == x
x = 5 {
    ...
}
x == x_
``` -->




## Functions
## Input/Output



<!-- Ignore the below notes:

``` c
// <= can be either way
// eg. x <= 1 is the same as 1 => x
// The side with the arrow head is modified to equal the other end
// Every stamp (anything that contains a '=') is evaluated to see if both sides are equal (looping).
// The body is run until the stamp is no longer true. Often this boolean is ignored


// TYPES
// int
// bool, float, string (self compiled)
// object/array: {}

// ASSIGNMENT
x <= 256                 // int
x <= True                // bool
x <= 25.6                // float
x <= "Hello, World!"     // string
x <= {1, 2, 3}           // array

// TERMINAL (IO)
terminal <= "Input your name: "      // print
name <= terminal                     // input

// WHILE LOOPS
1 == 1 {                // while true
    ...
}

x == y {                // while x is equal to y
    ...
}

false == (x == y) {     // while x is not equal to y
    ...
}

// IF STATEMENTS
temp <= x               // if statement
x == y {
    ...
    x <= temp + 1   // anything to make the check false
}
x <= temp

x_ <= x                 // if statement (prefered)
x_ == y {
    x_ <= x_ + 1
}

// FOR LOOPS
i <= 
``` -->

Headings that I link to but haven't written yet.
## Syntax error

## Runtime error