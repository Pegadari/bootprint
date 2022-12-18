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


## Stamping
The stamp is a powerful operator that can be used for assignment and can assist with control flow. A stamp consists of both a head (```=```, ```<```, ```>``` and ```!```) and a handle (```=```, ```:``` and ``` ```). The head prescribes the predicate for the truthiness of the stamp and the handle prescribes how the assignment is executed. The truth of the predicate is returned by the stamp statement.

The target variable is on the side of the head and the source expression is on the side of the handle. If the target variable is not a variable, a [syntax error](#syntax-error) will be thrown.

Below is an explaination of all twelve possible stamps with an example.

|      Example      |         Predicate         | New ```x``` value |
|:-----------------:|:-------------------------:|:-----------------:|
|   ```x == 5```    |      x is equal to 5      |         5         |
|   ```x <= 5```    |     x is less than 5      |         5         |
|   ```x >= 5```    |    x is greater than 5    |         5         |
|   ```x != 5```    |        x is not 5         |         5         |
|                   |                           |                   |
| ```x =: [5, 6]``` |    x is equal to [5, 6]   |         6         |
| ```x <: [5, 6]``` |   x is less than [5, 6]   |         6         |
| ```x >: [5, 6]``` |  x is greater than [5, 6] |         6         |
| ```x !: [5, 6]``` |      x is not [5, 6]      |         6         |
|                   |                           |                   |
|    ```x = 5```    |      x is equal to 5      |        N/A        |
|    ```x < 5```    |     x is less than 5      |        N/A        |
|    ```x > 5```    |    x is greater than 5    |        N/A        |
|    ```x ! 5```    |        x is not 5         |        N/A        |

Note that in Stamp, ```=``` is used for equality and ```==``` is used for assignment (and equality). This differs from convention.

<i>The handles ```+```, ```-```, ```*```, ```/```, ```%```, ```^```, ```|``` and ```&``` may be added to the specification in the future. These would operate similar to the '```_=```' in C language family.</i>


### Assignment
There are two types of assignment: direct and indirect. [Direct assignment](#direct-assignment) uses the ```=``` handle and [indirect assignment](#indirect-assignment) uses the ```:``` handle.

#### Direct assignment
Prescribed by the ```=``` handle, direct assignment works the same way as assignment in other languages.

For example, this assigns ```5``` to ```x``` and returns false (```0```) since ```x``` was not ```5``` (it was uninitaialised):
``` c
x == 5
```

And this assigns ```{3, 4}``` to ```x``` and returns true (```1```) since ```[1, 2] != [3, 4]```:
``` c
// initialise x and y
x == [1, 2]
y == [3, 4]

// assign y to x
x != y
```

#### Indirect assignment
Prescribed by the ```=``` handle, indirect assignment works similar to a for each loop, by looping over the values of the source expression, one by one.

For example,
``` c
x =: [1, 2, 3, 4]
```

Here, ```x``` is assigned to value of ```1```, then ```2```, then ```3```, then ```4```. It is not assigned the value of ```[1, 2, 3, 4]```.

### Control flow


## Structures
A well is a body of code wrapped in ```{ ... }``` ```{ ... }```. Wells are execute if the immediately preceding statement (often a stamp) is true and will continue to run until the statement is no longer true.

The following subsections demonstrate examples of Stamp code.

### While
If ```x``` equals ```5```, then the statment is true. While the statement is true, the well will continue to execute.
``` c
x = 5 {
    ...
}
```

### For
In Stamp, a for loop and a for each loop are equivalent.
``` c
x !: [1, 2, 3] {
    ...
    // runs three times, each with a different x value
}
```

**[ EXPLAIN HOW ```io```/```terminal``` WORKS ]**

The program,
``` c
x != [1, 2, 3] {
    io == x
}
```
outputs
```
123
```

### Conditional statements
This section include the family of if statements.

#### If
If ```x``` equals ```5```, then we want the well to execute, however, that would be a while loop. To emmulate an if statement, we only run the first iteration of a while loop. To do this, we use a temporary variable that we change during the first iteration. Changing the temporary variable will cause the condition to be false.

``` c
x_ == x
x_ = 5 {
    x_ == x_ + 1
    ...
}
```

#### If else
If ```x``` is not changed in the well, we can compare the temporary variable against ```x``` to see if it hasn't changed.
<!-- Again, we use a temporary variable to only execute the first iteration of the while loop. However, observe that if the 'if' well is executed, then the temporary variable will be changed. This will cause the 'else' clause to be false. If the 'if' well is not executed, then the temporary variable remains unchanged. To test is the temporary variable is changed, we can use a temporary constant, set to the initial value of ```x```. -->

``` c
x_ == x
// if
x_ = 5 {
    x_ == x_ + 1
    ...
}
// else
x_ = x {
    x_ == x_ + 1
    ...
}
```

If ```x``` is changed in the well, we will need a constant that holds the original value of ```x``` to compare against instead.
``` c
x_ == x
x__ == x
// if
x_ = 5 {
    x_ == x_ + 1
    ...
}
// else
x_ = x__ {
    x_ == x_ + 1
    ...
}
```

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