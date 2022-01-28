# Basics

## Setting a Project
```cmake
project( MyProject 
         VERSION <major>.<minor> 
         DESCRIPTION "Project Description"
         LANGUAGES <lang>
       )
```
- Arguments are positional.
- `MyProject` is the name of the current CMake project and is not optional.
    - The project is *not* a target.
- The other **keyword arguments** *are* optional.
- `LANGUAGES` can be any combination of the following: `C`, `CXX`, `Fortran`, `ASM`, `CUDA`, 
  `CSHARP`, and `SWIFT`.

## Making an Executable
```cmake
add_executable(Project, foo.cc, bar.cc, foo.h)
```
- `Project` is both the name of the CMake target as well as the name of the generated binary.
- Multiple source files can be given.
    - CMake is smart enough to ignore headers - in this example, nothing will be done with `foo.h`.
      Headers may be added here to trigger certain IDE features though, so headers are sometimes
      listed regardless.

## Making a Library
```cmake
add_library(foo STATIC foo.cpp bar.h)
```
- The first argument, `foo`, is the name of the new library target being created.
- `STATIC` indicates the type of linkage for the library, and can be `STATIC`, `SHARED`, or 
  `MODULE`.
    - `INTERFACE` is also a valid keyword, and indicates that nothing needs to be compiled;
    this is useful for header-only libraries, for example.
        - When using the `INTERFACE` keyword argument, *no filenames can follow.*
