# The Stamp Language Specification

<b>Disclaimer: The langauge is currently in development and is subject to major changes. Currently there are conflicts in the specification.</b>

Stamp is designed with minimal structures and overal boilerplate, with a specific yet powerful implentation of assignment and control flow - stamping.

## File format
Stamp programs are UTF-8 files with the ```.stamp``` file extension. It's just plain text.

## Comments
Unoriginally, all characters following a ```//``` are comments, just like the C family. There are no multiline comments.

## Types
Out of the box, Stamp only supports signed integers (64-bit), floats (64-bit, [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)), strings (UTF-8), booleans and arrays (denoted with ```[ ... ]```). A little limiting, but don't worry, it is dynamically typed.

### Length

## Stamping
The stamp is a powerful operator that can be used for assignment and can assist with control flow. The four valid stamps are ```<=```, ```=>```, ```==``` and ```<>```. These stamps are called 'left stamp', 'right stamp', 'ink stamp' and 'dual stamp' respectively.

An expression on the ```<```/```>``` side is a <u>target expression</u> and an expression on the ```=``` side is a <u>source expression</u>. For the dual stamp, both sides are target expressions and source expressions.



For example:
- ```target_variable <= source_expression```
- ```source_expression == source_expression```

The logic for stamping is described below.

1. <b>Determine the truthiness.</b> True if both sides are equal, false otherwise.
2. <b>Assignment.</b> Assign the target variable the value of the expression on the other side. For the dual stamp, this typically swaps their values. Skip this step for the ink stamp.
3. <b>Return the truthiness.</b>


There is a sub-step between Step 2 and Step 3 which will be covered in the for loop section.

(It can assign part or all of a variable onto another variable and returns the truthiness of the assignment. A stamp can point in either direction. For example, ```x <= y``` is the same as ```y => x```.)


### Assignment
There are two types of assignment: direct and indirect. Direct assignment occurs when the target variable is not initialised or when the two sides have equal [length](#length). Indirect assignment occurs when the 

The case of two unititialised variables in a dual stamp will result in a runtime error.

|                                    |      Left stamp     |     Right stamp     |   Ink stamp   |      Dual stamp     |
|------------------------------------|:-------------------:|:-------------------:|:-------------:|:-------------------:|
| Target expression contains literal |     syntax error    |     syntax error    |      N/A      |     syntax error    |
| Uninitialised source expression    | direct (deallocate) | direct (deallocate) | runtime error | direct (deallocate) |
| Same length                        |        direct       |        direct       |      none     |        direct       |
| Different length                   |       indirect      |       indirect      |      none     |       indirect      |

#### Direct assignment
If the target variable is not initialised or the target variable and the source expression have the same length, then stamping works the same way as assignment in other languages.
For example, ```x <= 5``` assigns ```5``` to ```x``` and the following assigns ```{3, 4}``` to ```x```.
```
// initialise x and y
x <= {1, 2}
y <= {3, 4}

// assign y to x
x <= y
```

#### Indirect assignment

### Control flow
If the target variable has a length less than the source expression, then...

The stamp also returns whether the target variable and source expression were equal. This is often ignored, but can be used for program flow.

## Structures
Also taken from the C family, ```{ ... }``` are used to wrap a body of code. This body is run if the immediately preceding statement (often a stamp) evaluates to true and will continue to run until the statement is no longer true.



### Conditional statements
#### If
If
```
x_ <= x
x_ == 5 {
    ...
    x_ <= x_ + 1
}
```
If not
```
x_ <= x
false == (x_ == 5) {
    ...
    x_ <= x_ + 1
}
```

#### If else
```
x_ <= x
// if
x_ == 5 {
    ...
    x_ <= x_ + 1
}
// else
x_ == x {
    ...
}
```

#### If elif else

### While
```
x == 5 {
    ...
}
```

### Do while
```
do <= true
do == true {
    ...
    fail_condition {
        do <= false
    }
}
```

### If while
```
x_ <= x
x <= 5 {
    ...
}
x <= x_
```

### For
In Stamp, a for loop and a for each loop are equivalent.
```
x <= {1, 2, 3} {
    ...
    // runs three times
}
```
```
x <= {2, 3}
x <= {1, 2, 3} {
    ...
    // runs twice
}
```


## Functions
## Input/Output



Ignore the below notes:

```
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
```