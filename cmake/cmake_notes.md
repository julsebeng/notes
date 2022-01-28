# Basic Concepts
CMake is a *build system generator* - that is, it generates config files for actual build systems (make, ninja, etc). *CMake does not compile any code itself!*

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

## Targets
Targets can be include directories, linked libraries, compile options, compile definitions,
compile features, and more. They're an important building block for modern CMake and should
be leveraged to the fullest extent!

```cmake
target_include_directories(Foo PUBLIC include)
```
- Add an include directory to target `Foo`.
- The keyword argument `PUBLIC` can be set to the following values in the following table. Note
  that, for excecutable targets this keyword variable may not mean much - on the flipside, it's
  a useful option for libraries.


| | |
|-|-|
|`PUBLIC`|All directories following the keyword argument will be used for the current target, as well as any other targets that depend on the current target.| 
|`PRIVATE`|All include directories will be used for the current target, but will not be passed on to dependencies of the current target.|
|`INTERFACE`|All include directories will *not* be used by the current target, but will still be passed to dependencies of the current target. Especially useful for libraries that don't require their own compilation, such as header-only libs.|

---

```cmake
# Create a new library target called Lib and provide source files for executable
add_library(Lib STATIC lib.cc lib.h)

# This command is tricky, read the notes below to fully understand how it works
target_link_libraries(Lib PUBLIC Foo)
```
- `target_link_libraries` takes a target (here, `Lib`) and adds a dependency if another is given
   (here, `Foo` from the previous example).
    - If no target `Foo` exists, CMake will add a link to a library called `Foo` on your path.
    - A full path to a library can also be provided, instead of a target name.
    - A linker flag can also be provided in lieu of a target name or
      path.
    - Older versions of CMake also allow the keyword argument (here,
      `PUBLIC`) to be skipped too, though an error will be raised if
      multiple "styles" of `target_link_libraries` are used in the 
      CMake file (maybe project too? Not sure).

| | |
|-|-|
|`PUBLIC`| Links the objects to the current target; any other targets that depend on the current object will also be linked to these objects.|
|`PRIVATE`|Links the objects to the current target, but dependencies of the current target will not be linked to these objects.|
|`INTERFACE`|Foo|


# Syntax Reference

# References
- [An Introduction to Modern CMake](https://cliutils.gitlab.io/modern-cmake/)