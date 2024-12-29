---
{"aliases":null,"tags":["go","ProgrammingLanguages/go","CrashCourse"],"projects":["loadgen"],"url":["https://www.youtube.com/watch?v=yyUHQIec83I","https://www.youtube.com/watch?v=XCZWyN9ZbEQ"],"type":"Video","Description":"A Crash course on how to start understanding GoLang","Areas":"HowTos","publish":true,"date created":"Thursday, December 26th 2024, 1:20:36 pm","date modified":"Thursday, December 26th 2024, 1:20:54 pm","PassFrontmatter":true,"created":"2024-12-26T13:20:36.584+05:30","updated":"2024-12-26T19:44:22.814+05:30"}
---

**Table of contents**
- [why ?](#why%20?)
- [What is it ?](#What%20is%20it%20?)
- [Core concepts](#Core%20concepts)
	- [Data type](#Data%20type)
			- [Type assignment magic](#Type%20assignment%20magic)
			- [Array](#Array)
			- [Slices](#Slices)
			- [Boolean DataType](#Boolean%20DataType)
			- [Map](#Map)
			- [Struct](#Struct)
	- [Loops](#Loops)
			- [For loop](#For%20loop)
	- [Conditional statements](#Conditional%20statements)
			- [if..else](#if..else)
			- [switch](#switch)
- [Function definition](#Function%20definition)
- [Built-in functions](#Built-in%20functions)
	- [len](#len)
	- [append](#append)
	- [make](#make)
	- [Strings package](#Strings%20package)
		- [contains](#contains)
	- [strconv](#strconv)
- [Packages in go](#Packages%20in%20go)
- [GoRoutine](#GoRoutine)


# why ?
- Easy to do multi-threading
	- Concurrency handling -> <u>GoRoutines</u>
- Compiled binary can be run on any platform. 
- Lots of highly scalable open-source projects are written in GoLang

# What is it ?
- Takes inspiration from C++ and python
	- i.e., syntax is simple and easy to read.
	- Fast to build and run, more efficient. 
- Backend language.

---
# Core concepts
## Data type
#### Type assignment magic
- If a datatype is created with immediate assignment, we don't need to explicitly mention the type. ie., ==implicit datatype assignment==
	- ` var test = "abc"; # this is fine.`
	- `var test; test = "abc" # This will throw an error. `
#### Array
```go
var test = [50]string {"a","b","c"}
```
#### Slices
Defining an array without a size basically creates a slice.
```go
var test []string
//or
var test = []string {"a","b","c"}

append(test,"first")
len(test)
```
#### Boolean DataType
```go
remainingTicketsStatus := tickets == 0
isValidName := len(firstName) >= 2 && len(lastName) <= 2
```
#### Map
- Needs to be initialized with the [[04-Resources/Processed/GoLang/Get started with GoLang#make\|#make]] function.
```go
var userData map[string]string
// Need to initialize the map
var userData = make(map[string]string)
userData["name"] = "John"
```
#### Struct
- It's similar to a data class in `Java` 
```go
type UserData struct {
	firstName string
	lastName string
	numberOfTickets uint
}

var userDataStruct = UserData{
	firstName:       "yesh",
	lastName:        "s",
	numberOfTickets: 10,
}
```

---
## Loops
#### For loop
- We get two variables in a for loop.
	- Index
	- Variable. 

```go
for i, v := range test {
	fmt.Println(i, v)
}
```
- Suppose you don't need to index. the above code will not run if you remove `i` from the print statement. 
```go
for _, v := range test {
	fmt.Println(v)
}
```
- `break` statement is same as `Java` 
- `continue` statement is same as `Java`
## Conditional statements
#### if..else
```go
// The () is optional in golang 
if (testint == 50) {
	fmt.Print("testint is 50")
} else if testint == 40 { 
	fmt.Print("testint is 40")
} else {
	fmt.Print("testint is not 50 or 40")
}
```

#### switch
- No need to add a `Break` statement after every `case` 
```go
switch city {
	case "New York", "Washington":
		//Do something
	case "Berlin":
		//Do something
	default:
		//Do something
}
```

---
# Function definition
- A function can return <span style="background:rgba(5, 117, 197, 0.2)">more than one output</span> in golang
- You pass variable first and then the type, unlike `Java` 
```go
func myfunc(p, q int)(int, int, int ){ 
    return p - q, p * q, p + q  
}
```

---
# Built-in functions
## len 
- Returns different outputs based on what datatype variable is passed to it.
- if `String` is passed, returns the length.
- if `Slice` is passed, returns size of the array.
## append
- Used to add stuff to a slice
	- `append(slice,"First")`
## make
- Used to initialize a map
	-  `var userData = make(map[string]string)`
- make an empty list of maps
	- `make([]map[string]string,0)`

## Strings package
### contains
```go
strings.Contains(email,"@");
```

## strconv
- Different functions to convert strings from and to other datatypes
- e.g., `strconv.FormatUint(uint64(xyz),10)` 
	- The `10` here refers to the base value. i.e., base 10

---
# Packages in go
- Define **which package** a file belongs to in the first line of the file.
	- Each package should have it's own dedicated folder just like how it's in `Java`
- Importing packages
	- Add the new packages to the `import` statement. 
	- We need to add the `module name\name of package` or `Go` will search its default repository for that package.
		- Get the `module name` from the `go.mod` file. 
- If the function name starts with a <span style="background:rgba(240, 167, 216, 0.55)">capital letter</span>, The function is a `public` function. 
	- You can also export variables with the same logic
---
# GoRoutine
- Just add a `go` keyword in front of the function call. That's it!
- All the thread-pool creation and cleanup is handled automatically
```go
go callingsSomeFn()
```
- We need to call the `sync` package if we want the main thread to wait till the GoRoutines are done.
	- `wg.Wait()` will wait till all the GoRoutines are done.
	- we need to add `wg.done()` inside of your function to notify the main thread that you are done. 
		- We add `wg.add(1)` before calling the GoRoutine. the `done` basically does a decrement on that. 

