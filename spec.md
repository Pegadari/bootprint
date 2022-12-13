# The Stamp Language Specification

<b>Disclaimer: The langauge is currently in development and is subject to mjor changes.</b>

Stamp is designed with minimal structures and overal boilerplate with a specific yet powerful implentation of assignment - stamping.

## File format
Stamp programs are UTF-8 files with the ```.stamp``` file extension. It's just plain text.

## Comments
Unoriginally, all characters following a ```//``` are comments.
eg.
```
// This is a comment
```

## Types
Out of the box, Stamp only supports signed integers and strings. Quite limiting at first, but don't worry, it is dynamically typed.

## Stamping
The signature of Stamp. A stamp (```<=```) is powerful operator that can be used for assignment and program flow. It can assign part or all of a variable onto another variable and returns the truthiness of the assignment. A stamp can point in either direction. For example, ```x <= y``` is the same as ```y => x```.

The target variable is on the side of the ```<```/```>``` and the source expression is on the side of the ```=```.

### Assignment
If the target variable is not initiated or the target variable and the source expression have the same length, then stamping works the same way as assignment in other languages.
For example, ```x <= 5``` assigns ```5``` to ```x``` and
```
// initialise x and y
x <= {1, 2}
y <= {3, 4}

// assign y to x
x <= y
```
assigns ```{3, 4}``` to ```x```.

### Program flow
If the target variable has a length less than the source expression, then...

The stamp also returns whether the target variable and source expression were equal. This is often ignored, but can be used for program flow.

## Structures

### If

### While

### For

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