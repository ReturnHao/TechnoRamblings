# Extern variable in CUDA & C/C++

**Warning: The use of global variables is discouraged!!!**

**Warning: The use of global variables is discouraged!!!**

**Warning: The use of global variables is discouraged!!!**



In C/C++, the **`extern`** keyword may be applied to a global variable, function, or template declaration. It specifies that the symbol has *external linkage*.

The **`extern`** keyword has four meanings depending on the context, and two for variable:

- In a non-**`const`** global variable declaration, **`extern`** specifies that the variable or function is defined in another translation unit. The **`extern`** must be applied in all files except the one where the variable is defined.
- In a **`const`** variable declaration, it specifies that the variable has external linkage. The **`extern`** must be applied to all declarations in all files. (Global **`const`** variables have internal linkage by default.)

```C++
//fileA.cpp
int i = 42; // declaration and definition

//fileB.cpp
extern int i;  // declaration only. same as i in FileA

//fileC.cpp
extern int i;  // declaration only. same as i in FileA

//fileD.cpp
int i = 43; // LNK2005! 'i' already has a definition.
extern int i = 43; // same error (extern is ignored on definitions)
```

## *How to use global C++ variables in CUDA files(discouraged)*

Due to the [CUDA C Programming Guide](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#device-memory-space-specifiers), The `__device__`, `__shared__`, `__managed__` and `__constant__` memory space specifiers are not allowed on:

- `class`, `struct`, and `union` data members,
- formal parameters,
- non-extern variable declarations within a function that executes on the host.

The `__device__`, `__constant__` and `__managed__` memory space specifiers are not allowed on variable declarations that are neither extern nor static within a function that executes on the device.

When compiling in the whole program compilation mode (see the nvcc user manual for a description of this mode), `__device__`, `__shared__`, `__managed__` and `__constant__` variables **cannot be defined as external using the `extern` keyword**. The only exception is for dynamically allocated `__shared__` variables as described in [index.html#__shared__](https://docs.nvidia.com/cuda/cuda-c-programming-guide/shared).

When compiling in the separate compilation mode (see the nvcc user manual for a description of this mode), `__device__`, `__shared__`, `__managed__` and `__constant__` variables **can be defined as external using the `extern` keyword**. `nvlink` will generate an error when it cannot find a definition for an external variable (unless it is a dynamically allocated `__shared__` variable).

However, even if they can be defined as external, entities there seems to be separated ([CUDA Compiler Driver NVCC](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#nvcc-options-for-separate-compilation) gives an example in this mode, **Warning: Local CMAKE Practiced Only, Need Further Discussion**). 



Hence, in mixed programming with C++, CUDA does not support so-called extern variables, so they should be avoided and replaced by parameter passing by functions.