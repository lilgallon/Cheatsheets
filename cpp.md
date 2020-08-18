**work in progress**

## 1. Things to know
*or that you should not forget*

- `0 == false`

## 2. Common mistakes

- `if(a = x)` instead of `if(a == x)`
- `int* a, b;` instead of `int *a, *b;`

## 2. Linker and compiler

*todo*

## 3. Pre-processed statements

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

## 4. Declarations & definitions

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

## 5. Debugging

*todo*

## 6. Pointers & references

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