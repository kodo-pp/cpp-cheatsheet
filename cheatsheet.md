---
geometry: 'top=2cm, bottom=1.5cm, left=1cm, right=1cm'
fontsize: 12pt
...

# C++ cheatsheet

## Project structure
Your project typically contains one or several `.cpp` files --- source files
--- and zero or more `.h` files --- header files. It might be good to organize
them in several folders, for example in the following way:

    .
    ├── include
    │   ├── bar.h
    │   └── foo.h
    └── src
        ├── bar.cpp
        ├── foo.cpp
        └── main.cpp

(here all source files are in the `src` directory and all header files are in
`include` directory). Of course, you can organize the files in any way you
want, you don't have to follow this pattern.

We can see the following pattern: each source file (except main.cpp) has a
corresponding header file (`src/bar.cpp` -> `include/bar.h`; `src/foo.cpp`
-> `include/bar.h`). This is on purpose.  In `main.cpp` you usually want to
use some functions defined in other files. But `main.cpp` must know what
parameters do these functions take and what do they return. There are two
ways of doing this.

First, you can **declare** (provide compiler with a "description" of a
function) functions in `main.cpp` which are **defined** (implemented;
code written for them) in some other file. *[Note: remember there terms
(declare/declaration and define/definition). They are crucial to understand
what will be going on next]*.

Consider the following example:

`src/main.cpp`:

```cpp
#include <iostream>

int add_five(int x); // Declaration of add_five function


int main()
{
    int value = 14;
    std::cout << add_five(value) << std::endl;  // Prints 19
}
```

`src/add_five.cpp`:

```cpp
int add_five(int x) // Definition of add_five function
{
    return x + 5;
}
```

We have the `add_five` function in `src/add_five.cpp`. In order to use
it in `src/main.cpp`, we have to declare it there (in `src/main.cpp`). The
declaration and the definition must match, so you cannot write something like
`int function(int x) { ... }` in one file and declare it as `void function();`
in another file --- neither the number of arguments, nor the return type
match in this case.

However, declaring all functions you use may be inconvenient and require you
to write too much code, especially when your project grows bigger than just
3-4 files.  The solution to this lies in the second option: using header
files (`.h` files). The idea is: let us (1) move all the declarations into
the header file, and (2) when you want to use a function from another file,
you include corresponding header file instead of writing declarations.

Let us modify our last example to use header files:

`src/main.cpp`:

```cpp
#include <iostream>

// Here we include our header file. Notice the use of #include "file"
// instead of #include <file>. At least for now, until we study more
// complex things like compiler options, use quotes ("")
// for YOUR header files and angle brackets (<>) for SYSTEM AND STANDARD
// LIBRARY header files.
#include "include/add_five.h"
// Notice that we removed the declaration of add_five function from here.
// It is because this declaration is already provided by include/add_five.h
// header file.

int main()
{
    int value = 14;
    std::cout << add_five(value) << std::endl;  // Prints 19
}
```

`src/add_five.cpp`:

```cpp
// Important: we don't have to include this file here. If we do, it will
// be the same as first declaring a function and then defining it. If we
// don't, we will stay only with the definition.
//
// However, the best practice is TO INCLUDE the corresponding header file,
// because it will allow the compiler to check that the declaration
// matches the definition.
//
// If you don't understand what is written above, don't worry, it is not
// required that you understand it. In this case just make sure to include
// the header file when you use a certain function AND when you define it
#include "include/add_five.h"

int add_five(int x) // Definition of add_five function
{
    return x + 5;
}
```

`include/add_five.h`:

```cpp
// Here are all our declarations of functions in src/add_five.cpp. In this
// very case we have only one function in src/add_five.cpp, so there is
// only one declaration
int add_five(int x);
```

Now when you want to use `add_five` function, you don't have to write
it's declaration. You can just include the `include/add_five.h` file
instead. Important: when you include a header file, you only have to write
`#include "include/some_file.h"` **only once per file**. For example, if you
use `add_five` function, say, 5 times in `src/main.cpp`, you only need to write
`#include "include/add_five.h"` **once**. If you also use this function in
`src/bar.cpp`, you have to write `#include "include/add_five.h"` also in
`src/bar.cpp`, but again, only once.

I believe, that's it. Things to remember:

- Recall the difference between declaration and definition. It is really
important
- Include files contain **declarations** of functions, while source files
contain their **definitions**
- Never include `.cpp` files. Only `.h` (or `.hpp`, which is just another
extension for header files)
