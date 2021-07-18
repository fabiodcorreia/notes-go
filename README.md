# Notes About Golang

## Numbers

### Which integer should I use?

Unless we need to be explicit about the size or sign of an integer for performance or integration purposes, use the int type, any other type should be considered premature optimization until proven otherwise. With that in mind, we have the following 3 rules of thumb.

1. When working with a binary file format or network protocol that has an integer of a specific size or sign, use the corresponding integer type.
2. When writing a library function that should work with any integer type, write a pair of functions, one with in64 for the parameters and variables and another with uint64.
3. For all the other cases, just use the int type

## Arrays and Slices

### Which should I use ?

Even looking similar at the code level, they are different:

#### Array

- Fixed size - after initialization the size will not change.
- Contiguous memory allocation - a `[4]int` will allocate memory for 4 integers one after the other even if we only use one position of the array.
- It's not a pointer to the first element of the array, it's a value so when we assign or pass around an array it makes a copy of it.

#### Slice

- Dynamic size - after initialization, the size can increase to accommodate more elements.
- Internally it's a pointer and an array, the pointer point to the array and when the size needs to change a new array is created, the values are copied to the new array and the pointer start to point to the new array.

### When initialize a slice with nonzero length ?

1. If a slice is used as a buffer, then specify a nonzero length.
2. If the exact size for the slice is known, then we can specify the length. The downside is that if the size is wrong we will have positions with zero values at the end.
3. In other situations, use the zero length but specify the capacity. This allows to use append to add more items without the need to create a bigger array right the way and no zero values at the end.

**Note** The `append` function always increases the length of a slice, so if we set the length to a nonzero value and use `append without filling the previous positions, it will end up with zero values at the beginning of the slice.

### Slicing a Slice?

Slicing a slice is nothing more than creating a new slice with a subset of another slice. Still we need to be aware that the new slice shares the same memory as the original one, so if we change the elements of the new slice it will change the elements of the original.

- `s2 := s1[:2]` - from index 0 to index 1 or the frist 2 elements.
- `s2 := s1[1:]` - from index 1 to the end or all the elements except the first.
- `s2 := [1:3]`- from index 1 to 2 or the second and third elements.
- `s2 := [:]` - from index 0 to the end or all the elements.

Because the memory is shared, we shouldn't use `append` to the new slice, because it will also append to the original slice overwriting values. If we intend to modify the new slice, we can `copy` the slice, so they will not share the memory anymore.

## Variable Declaration

### Variables at package level

Package level variables should be avoided, variables should be declared inside functions unless they are immutable.

### Which variable declaration style should I use?

We can declare variables in two ways, using the `var` keyword or  `:=` sign, the most common declaration style is using the `:=` but there are some exceptions.

1. Declaring a variable outside a function (package level) it's only allowed with `var`.
2. Initializing a variable to it's zero value, we should use `var x int` instead of `x := 0`.
3. Assigning an untyped constant or a literal to a variable and the default type for the constant or literal isn't the type we want for the variable we use `var x byte = 20`to reinforce the type.
4. Since `:=` allows assigning to both new and existing variables, it sometimes creates new variables when we think that we are reusing existing ones



## Control Structures

### Break on Switch?

One major difference between the `switch` in Go and other languages like C or Java is that Go only runs the selected case and not all the cases that follow it. For that reason we don't need to use a `break` after each `case`.

### Choosing between blank switch and if

If we have a chain of if/else we should use a blank instead, it makes the code more readable.

```go
switch {
  case x == 1: //action 1
  case x == 2: //action 2
  case x == 3: //action 3
}
```

Instead of

```go
if x == 1 {
  // action 1
} else if x == 2{
  // action 2
} else if x == 3{
  // action 3
}
```

### Four ways off for

We can write a for statment in four different way:

1. Complete or C Style `for i := 0; i < 10; i++ { ... }`
2. Condition Only `for i < 10 { ... }`
3. Infinite `for{ ... }`
4. Range `for i, v := range values { ... }`

The `range` is similar to the `forEach` on other languages, it will iterate hover the entire slice of elements and for each iteration it returns the index and a copy of the element on the index position. Since `v` is a copy of the original element, if we change `v` it will not reflect on the original element unless it's a slice of pointers.





