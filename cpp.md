*work in progress*
- [1. Introduction](#1-introduction)
	- [1.1 Things to know](#11-things-to-know)
	- [1.2 Common mistakes](#12-common-mistakes)
- [2. Linking and compilation](#2-linking-and-compilation)
	- [2.1 Linker and compiler](#21-linker-and-compiler)
	- [2.2 Pre-processed statements](#22-pre-processed-statements)
- [2.3 Declarations & definitions](#23-declarations--definitions)
- [3. Debugging](#3-debugging)
- [4. Basic pointers, references and smart pointers](#4-basic-pointers-references-and-smart-pointers)
	- [4.1 Basic pointers & References](#41-basic-pointers--references)
- [4.2 Smart pointers](#42-smart-pointers)
- [5. Oriented object programming (OOP)](#5-oriented-object-programming-oop)
	- [5.1 Visibility](#51-visibility)
	- [5.2 Friend](#52-friend)
	- [5.3 Polymorphism](#53-polymorphism)
- [6. Instantiating objects](#6-instantiating-objects)
	- [6.1 Initializer lists](#61-initializer-lists)
	- [6.2 How to properly instantiate objects](#62-how-to-properly-instantiate-objects)
	- [6.3 new Keyword](#63-new-keyword)
	- [6.4 Implicit conversion and explicit keyword](#64-implicit-conversion-and-explicit-keyword)
	- [6.5 Object lifetime](#65-object-lifetime)
	- [6.6 Copy constructor](#66-copy-constructor)
- [7. Types](#7-types)
	- [7.1 Arrays](#71-arrays)
	- [7.2 Strings](#72-strings)
	- [7.3 Enums](#73-enums)
- [8. Keywords](#8-keywords)
- [8.1 Const](#81-const)
- [8.2 Mutable](#82-mutable)
- [8.3 New](#83-new)
- [8.4 This](#84-this)
- [9 Operators](#9-operators)
	- [9.1 Arrow operator](#91-arrow-operator)
- [10. Templates](#10-templates)
- [11. Operator overloading](#11-operator-overloading)

## 1. Introduction

### 1.1 Things to know
*or that you should not forget*

- `0 == false`
- **singleton**: class that will only be instantiated once
- **ternary operator**: `(bool expr) ? (value if true) : (value if false)`

### 1.2 Common mistakes

- `if(a = x)` instead of `if(a == x)`
- `int* a, b;` instead of `int *a, *b;`


## 2. Linking and compilation

### 2.1 Linker and compiler

*todo*

### 2.2 Pre-processed statements

They start with `#` and are evaluated before the compilation. There are several preprocessor directives, let's see what they do:

- `#define NAME VALUE`: it will replace all the occurences of `NAME` by `VALUE`

```c++
#define INTEGER int

INTEGER a = 0; // "INTEGER" will be replaced by "int" before compilation
```

- `#include`: It copies and pastes the content of the included file to the current file. That's as simple as that. For example, you can include a closing curly bracket, and it will work:

```c++
// start file1.cpp
}
// end file1.cpp

// start file2.cpp
int Main()
{
    return 0;
#include <file1.cpp>
// end file 2.cpp
```

The difference between `#include <...>` and `#include "..."` is that when you use `<>` it will search for the file in the include path while `""` will look for the file in the include path as well as relatively to the current file.

- `#pragma once:` It means "only include this file once". It prevents having duplicate declarations. Here is what you probably already saw. It has the exact same effect:

```c++
// file.h
#ifndef _FILE_H
#define _FILE_H
// code ...
#endif
```
What it does is that when the code is copied for the first time, `#ifndef _FILE_H` is evaluated as `true` because `_FILE_H` has not been defined yet. Then, `#define _FILE_H` defines it. So when the code is copied a second time in the same file, `#ifndef _FILE_H` will be evaluated as `false`, and the code won't be copied.

- `#if`, `#elif`, `#else` and `#endif`: Those are the conditional pre-processed statements. As long as you know how to use if, else if, and else you should be good.

- `##`: Used to concat two tokens: `x##y`

## 2.3 Declarations & definitions

**Declaration**: It tells the compiler that the declared variable / function (whatever) exists. It is a sort of promise that you make to the compiler. So if the declaration does not refer to anything, then there will be an issue during runtime saying unable to resolve symbol which is a linker error.

```c++
// We said that GetAbsoluteFilePath exists, but we do not define what it does
// -> This is a declaration.
std::string GetAbsoluteFilePath(std::string filename);
```

**Definition**: It basically defines what the declaration declared.

```c++
// We define what GetAbsoluteFilePath does
// -> This is a definition.
std::string GetAbsoluteFilePath(std::string filename)
{
	std::string path = "";

	for (const auto& entry : std::filesystem::directory_iterator("."))
	{
		if (entry.path().filename().string() == filename)
		{
			path = std::filesystem::absolute(entry.path()).string();
			break;
		}
	}

	return path;
}
```

## 3. Debugging

> todo

## 4. Basic pointers, references and smart pointers

### 4.1 Basic pointers & References

The addresses are `8 bytes` on `x64` architectures, and `4 bytes` on `x32` architectures (also knwon as `x86`). An address is an hexadecimal number. To represent an hexadecimal number, we use the `0x` prefix.

You need to know that by heart:
- `type* ptr` defines `ptr` as being an address pointing to a variable of the given `type`
- `*ptr` reads what is at the address pointed by ptr (it's called dereferencing)
- `&var` gives the memory address of var

Let's see all of that in action
```c++
// This instruction creates a pointer called ptr. ptr is an hexadecimal number representing an address (ex: 0x1234 for x32).
// As you can see, ptr is an address, it is not equal to 5. So by using int*, you make a promise that this address points at an integer. This will then be used when deferencing to read and write what's contained at this address.
int* ptr = new int(5);

// So you can do that. It will change the address to 4, which is wrong (can not convert from 'int' to 'int *')
// ptr = 4;

// If you want to update the variable, you need to dereference the pointer first
*ptr = 4;

// This will print the address where the address pointing to the integer is located
std::cout << std::hex << &ptr << std::endl;

delete ptr;

// If you understand that completly, you know that you can do a lot of weird stuff by manipulating addresses and types
```

We can also use `&` to create **aliases**.

```c++
int var = 5;
int &ref = var;

ref ++;
// var = 6
// ref = 6
```

Here is an other use of `&`:

```c++
void funct(int var) {
    ++ var;
}

int var = 0;
funct(var);
// a is still 0
funct(&var)
// a is now1
```

In the first call of `funct`, a **copy** of `var` will be passed. So it's an other variable that will be updated, and not `var` itself. However, in the second call of `funct`, `a` is not copied, it is passed as **reference**. This reference will be updated, and as effect, `var` will also get updated. It is actually the exact same thing as:

```c++
void funct(int* var) {
    var = (*var);
    ++ var;
}
```

It is just **syntax sugar**.

## 4.2 Smart pointers

> todo

## 5. Oriented object programming (OOP)

### 5.1 Visibility

- `private`: only current class and friends can access it
- `protected`: only current class and children classes can access it
- `public`: anything can access it
- nothing: default visibility is private

### 5.2 Friend

> todo

### 5.3 Polymorphism

> todo: Different types of functions (pure virtual, virtual, ...)

## 6. Instantiating objects

### 6.1 Initializer lists

> todo (explain why it's better than initializing the attributes in the constructor body)

### 6.2 How to properly instantiate objects

> todo (if we use C++, it's for the optimizations!)

### 6.3 new Keyword

> todo (explain what it does)

### 6.4 Implicit conversion and explicit keyword

> todo

### 6.5 Object lifetime

> todo

### 6.6 Copy constructor

> todo

## 7. Types

### 7.1 Arrays

> todo ("basic arrays" , dynamic arrays, where they're good and bad at. How to chose the array to use?) - also how to PROPERLY use std::vector

### 7.2 Strings

> todo (a lot of stuff to say here): char*, std::string, string literals, ...

### 7.3 Enums

> todo

## 8. Keywords

## 8.1 Const

> todo (how should we use it, advantages, ...)

## 8.2 Mutable

> todo: (related to const, but can be used for lambdas and so on)

## 8.3 New

The new keyword is detailed in [Instantiating objects](#63-new-keyword)

## 8.4 This

> todo

## 9 Operators
### 9.1 Arrow operator

> todo

## 10. Templates

> todo (purpose, how does it work, ...)

## 11. Operator overloading

> todo

