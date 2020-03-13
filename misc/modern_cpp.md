# Modern C++ (and some older stuff too)

- [The Build Pipeline](#the-build-pipeline)
- [`auto`](#the-auto-keyword)
- [Template Non-type Parameters](#template-non-type-parameters)
- [Partial Template Specialization](#partial-template-specialization)
- [Type Aliases and Alias Templates](#type-aliases-and-alias-templates)
- [Uniform Initialization](#uniform-initialization)
- [Non-static Member Initialization](#non-staticmember-initialization)
- [Scoped Enumerations](#scoped-enumerations)
- [Misc. C++ Features](#misc-features)

---

## The Build Pipeline
Each source file (aka Translation Unit) will be compiled into its own object file. These discrete object files are then linked
together to form an executable, a shared library, or a static library.
- A static library in reality, is nothing more than an archive of object files.

Whenever a header is included in a source file, the C++ preprocessor will look inside that file and simply replace the `#include`
directive in the source file with the contents of the included header file. Only source files are passed to the compiler, no
headers.
- Note that the temporary source file, known as an expanded source code file, can be created in `g++` as so:
    ```
    g++ -Wall -std=c++14 -E file.cc
    ```
- `nm foo.o` can be used to view the symbols that an object file exports.
    - `T` in front of the symbol name indicates that it's exported as part of the `.text` segment.

---

## The `auto` Keyword
When using `auto`, the compiler wil deduce the type using the following criteria:
- From the type of the expression used to initialize a variable when used to declare variables.
- From the trailing return type of the type of the return expression of a function, when `auto` is used in place
  of the return type of a function. For lambda functions, `auto` can also be used for arguments.

There are cases where the compiler does not deduce the appropriate type. For example, a `char const *` (syntactically equivalent
to `const char *`) may be deduced, when the intention was to use `std::string`.

#### Benefits
- Enforced variable initialization; `auto foo;` is invalid because there is no information available for type deduction.
- Prevention of implicit type conversions:
    ```c++
    int a = some_vector.size();  // Implicit conversion: size_t -> int
    auto b = some_vector.size(); // No implicit conversion, b is deduced to size_t
    ```
- Promotes good OOP practices by avoiding explicit type declarations and instead allowing variables to be defined
  generically.
- Saves time - there are many cases where a type is needed but not really cared about, the canonical case being iterators.
- Prevents redundancy. Often when dynamically creating objects, the type is needed on both the left and right sides of the
  assignment:
    ```c++
    // Consider:
    auto foo = std::make_shared< std::string >();
    // Versus:
    std::shared_ptr< std::string > foo = std::make_shared< std::string >();
    ```

#### Pitfalls
- `auto` does not deduce CV qualifiers nor reference types:
    ```c++
    {
        int& some_func();

        auto x = some_func() // Will deduce to type int, not int&
        auto& x = some_func() // Will deduce to type int&
    }

    // Problematic in this scenario, where auto is used to deduce the return type:
    {
        int& some_func();
        auto some_func_proxy() { return some_func(); } // auto deduces to int, not int&

        auto& x = some_func_proxy(); // Error: cannot convert from int to int&
    }
    // Solution 1: return auto&
    {
        int& some_func();
        auto& some_func_proxy() { return some_func(); } // Property deduces to int& return type

        auto& x = some_func_proxy();
    }

    // Solution 1: use decltype; this method allows more generic forwarding, as it doesn't care if if
    // the function  is returning a value or a reference.
    {
        int& some_func();
        decltype( auto ) some_func_proxy() { return some_func(); } // Property deduces to int& return type

        decltype( auto ) x = some_func_proxy();
    }
    ```

- `auto` does not deduce multi-word types, such as `long long`. Fortunately, some types have multiple type names that are
  a single word, such as `llong`.
    ```c++
    auto l1 = long long{ 1000 }; // Invalid
    auto l2 = llong{ 1000 }      // Valid
    auto l3 = 1000LL;            // Valid
    ```
- `auto` can sabotage type information that an IDE may reveal to a programmer, requiring them to manually deduce the type
  for themselves.

---

## Template Non-type Parameters
In addition to template type parameters, template classes & functions can also use non-type parameters as a template argument:
```c++
template< class T > // T is a template type parameter
class ClassA { /* ... */ };

template< int size > // size is a non-type parameter
class ClassB { /* ... */ };
```
A non-type parameter can be:
- A value that has an integral type or enumeration.
- A pointer or reference to a class object.
- A pointer or reference to a function.
- A pointer or reference to a class member function.
- `std::nullptr_t`

#### Benefits
Non-type template parameters allow for compile-time substitution of the templated type. For example:
```c++
template <class T, int size> // size is the non-type parameter
class StaticArray {
private:
    // The non-type parameter controls the size of the array
    T m_array[size];
};

// declare an integer array with room for 12 integers; m_array of size 12 is statically allocated.
StaticArray<int, 12> intArray;
// declare a double buffer with room for 4 doubles; m_array of size 4 is statically allocated.
StaticArray<double, 4> doubleArray;
```

---

## Partial Template Specialization
In some cases, it may be desirable to specialize some behavior with a templated function/class depending on the types of its
template parameters. Full specialization is an option:
```c++
template <class T, int size> // size is the expression parameter
class StaticArray
{
private:
	// The expression parameter controls the size of the array
	T m_array[size];

public:
	T& operator[](int index)
	{ return m_array[index]; }
};

// Print out each element of the array, with a space afterwards
template <typename T, int size>
void print(StaticArray<T, size> &array)
{
	for (int count = 0; count < size; ++count)
		std::cout << array[count] << ' ';
}

// Override print() for fully specialized StaticArray<char, 14>; don't print a space between each element
template <>
void print(StaticArray<char, 14> &array)
{
	for (int count = 0; count < 14; ++count)
		std::cout << array[count];
}
```
Full template specialization is obviously brittle - in this case, only `char` arrays of size 14 will be specialized to print
without a space.

The alternative is to use partial template specialization:
```c++
template <class T, int size> // size is the expression parameter
class StaticArray
{
private:
	// The expression parameter controls the size of the array
	T m_array[size];

public:
	T& operator[](int index)
	{ return m_array[index]; }
};

// Print out each element of the array, with a space afterwards
template <typename T, int size>
void print(StaticArray<T, size> &array)
{
	for (int count = 0; count < size; ++count)
		std::cout << array[count] << ' ';
}

// Override print() for partially specialized printing.
template <int size>
void print(StaticArray<char, size> &array)
{
	for (int count = 0; count < 14; ++count)
		std::cout << array[count];
}
```

#### Pitfalls
**In C++14, only templated classes can be partially specialized, not templated functions.** The reason why the `print()`
specialization worked in the above example is because the function *itself* was not partially specialized, it was just overloaded
to take a specific, partially templated class `StaticArray< char, size >`.

This restriction creates challenges when dealing with member functions instead of free functions. If the `print()` function
above was instead a free function, the following partial function specialization would not work:
```c++
template <int size>
void StaticArray<double, size>::print()
{
	for (int i = 0; i < size; i++)
		std::cout << std::scientific << m_array[i] << " ";
	std::cout << "\n";
}
```
There are ways around this such as using a common base class that the generic template class inherits from as well as the
specialized template class, such as the examples [here](https://www.learncpp.com/cpp-tutorial/137-partial-template-specialization/)
.

---

## Type Aliases and Alias Templates
Type aliases follow the form `using identifier = type-id`:
```c++
using byte    = unsigned char;
using pbyte   = unsigned char *;
using array_t = int[ 10 ];
using fcn     = void( byte, double );
```

Template aliases follow the form `template< template-params-list > identifier = type-id`:
```c++
template< class T >
class custom_allocator { /* ... */ };
template< typename T >
using vec_t = std::vector< T, custom_allocator< T > >;

vec_t< int > foo;
vec_t< std::string > bar;
```

There is the older `typedef` syntax that C++ uses, that accomplishes much of the same task:
```c++
typedef unsigned char byte;
typedef unsigned char* pbyte;
typedef int array_t[ 10 ];
typedef void( *fn )( byte, double );
```
- `typedef` aliases follow the same name hiding rules as other identifiers - that is, if an identifier in a nested scope
  redefines the `typedef`, it will hide the previous definition:
  ```c++
  namespace A {
      typedef int Foo;
  }

  namespace B {
      using namespace A;
      typedef char Foo;
  }

  cout << typeid( B::Foo ).name() << endl; // Output: c
  cout << typeid( A::Foo ).name() << endl; // Output: i
  ```
- `typedef` allows the redefinition of an alias, but only if it refers to the same type:
    ```c++
    typedef int Foo;
    typedef int Bar;

    typedef Bar Foo; // Valid, refers to the same underlying type
    typedef char Foo; // Invalid, does not refer to the same underlying type
    ```
#### Benefits
- `using` definitions are more readable than `typedef` definitions. `typedef` function syntax is also consistent with
  `std::function` type declarations:
   ```c++
   std::function< void( int, int ) >;

   // Consider
   using fcn = void( int, int );
   // Versus:
   typedef void( *fcn )( int, int );
   ```
- The main advantage of `using` over `typedef` is it allows for template aliases, but there are some restrictions:
    - Alias templates cannot be partially or fully specialized.
    - Alias templates aren't deduced by template argument deduction when deducing a template parameter.
    - The type produced when specializing an alias template is not allowed to directly or indirectly make use of its own type.

---

## Uniform Initialization
Uniform initialization (using `{}` initialization) can be used for direct and copy list initialization:
```c++
// Direct initialization
T object { data };
// Copy list initialization
T object = { data };
```

Prior to C++11, different types of objects required different methods of initialization:
- Fundamental types were initialized with assignment: `int a = 2`.
- Class objects could also be initialized with assignment if they possessed a single-argument constructor:
    ```c++
    class foo {
            int a_;
        public:
            foo( int a ) : a_( a ) {}
    };

    foo f1 = 2;
    ```
- Non-aggregate classes could be initialized with parentheses.
    - An aggregate class has no user-defined constructors, no private or protected non-static data members,
      no base classes, and no virtual functions; typically this is a `struct` or `union`.
    ```c++
    class foo {
            int a_;
            int b_;
        public:
            foo(): a_( 0 ), b_( 0 ) {}
            foo( int a, int b = 0 ) : a_( a ), b_( b ) {}
    };

    foo f1;
    foo f2( 1, 2 );
    foo f3( 42 );
    foo f4(); // This is interpreted as a function declaration
    ```
- Aggregate and POD (plain old data) types could be initialized with brace-initialization.
    ```c++
    struct foo {
        int a;
        int b;
    };

    foo f1{ 1, 2 };

    int a[] = { 1, 2, 3, 4, 5 };
    ```
- Standard containers had no method of initialization; the object had to be created first, then have elements inserted
  into it ( except for vectors, as it's possible to assign values from an array, which can be initialized with aggregate
  initialization ).

In C++11 onwards, standard containers can use uniform initialization because they received an additional constructor that
takes a `std::initializer_list< T >` as an argument.
    - Initializer lists act like a proxy over an array of type `const T`:
        1. The compiler resolves the type of elements in the initializer list.
        2. The compiler creates an array with the elements in the initializer list.
        3. The compiler creates a `std::initializer_list< T >` object to wrap the created array.
        4. The `std::initializer_list< T >` is passed as an argument to the constructor.
    - User-defined classes can also take advantage of initializer lists. Note that when brace initialization is done,
      the initializer list constructor will take precedence over other constructors:
    ```c++
    class foo {
            int a_;
            int b_;
        public:
            foo(): a_( 0 ), b_( 0 ) {};
            foo( int a, int b = 0 ) : a_( a ), b_( b ) {};
            foo( std::initializer_list< int > l ) {};
    };

    foo f{ 1, 2 }; // Calls initializer list constructor

    // Free functions can also take initializer lists, and follow the same precedence rules.
    void func( const int a, const int b, const int c ) {
        // ...
    }
    void func( std::initializer_list< int > l ) {
        // ...
    }

    func( { 1, 2, 3 } ); // Calls initializer list overload
    ```

### Limitations

- Initializer list constructors preempting and hiding other constructor implementations can result in undesired behavior:
```c++
std::vector< int >( 5 ); // Creates a vector with room for 5 elements
std::vector< int >{ 5 }; // Creates a vector of one element: 5
```

- Initializer lists do not allow narrowing conversions:
```c++
int i { 1.2 }; // Error
int i { static_cast< int >( 1. 2 ) }; // OK
double d = 37 / 123; // OK
float f1{ d }; // Error
float f1{ static_cast< float >( d ) }; // OK
float f2{ 47/13 }; // OK
```

- Note that using `auto` with bracket initialization can have undesired effects (pre-17):
```c++
auto a = { 1 }; // Deduced to std::initializer_list< int >
auto b { 1 }; // Deduced to std::initializer_list< int >
auto c = { 1, 2 }; // Deduced to std::initializer_list< int >
auto d { 1, 2 }; // Deduced to std::initializer_list< int >
```
    - With C++17, some of these rules have changed:
        1. For copy list initialization, `auto` will deduce `std::initializer_list<T>` if all elements in the list have the
           same type, or be ill-formed.
        2. For direct list initialization, `auto` will deduce a `T` if the list has a single element, or be ill-formed if there
           is more than one element.

Thus, the rules for C++17 will cause the following changes:
```c++
auto a = { 1 }; // Copy list initialization, deduced to std::initializer_list< int >
auto b { 1 }; // Direct list initialization, deduced to int
auto c = { 1, 2 }; // Copy list initialization, deduced to std::initializer_list< int >
auto d { 1, 2 }; // Error, direct list initialization with more than 1 element
```

---

## Non-static Member Initialization
Non-static member data ought to be initialized in a constructor's initializer list. Initializing member variables in the body
of the constructor creates unnecessary overhead, as members are first default initialized (as they weren't explicitly
initialized in the initializer list), then assigned a value in the constructor body.

If a non-static member is is given a value in the declaration of a class, that is the value it will be initialized to when
the object is constructed; this is intended for members whose default values do not depend on the way the object is constructed.
**Note that values specified in an initializer list take precedence over default member initialization.**

To declare non-static members of a class you should:
1. Use default member initialization for providing default values for members of classes with multiple constructors that would use
   a common initializer for those members:
   ```c++
   struct Control {
        TextVAlignment valign = TextVAlignment::Middle;
        TextHAlign halign = TextHAlign::Left;
        std::string text;

        // constructors don't have to worry about valign or halign being set
        Control() : text( std::string{ "foo" } ) {};
        Control( const std::string& s ) : text( s ) {};
   };
   ```

2. Use default member initialization for constants, both static and non-static.
    ```c++
    struct Control {
        const int DefaultHeight = 14;
        const int DefaultWidth = 30;
    };
    ```

3. Use the constructor initializer list to initialize members that don't have default values, but depend on constructor
   parameters.
   ```c++
   struct Control {
        std::string text;
        Control() : text( std::string{ "foo" } ) {};
        Control( const std::string& s ) : text( s ) {};
   };
   ```

4. Use assignment in constructors when the other options are not available:
    - If data needs to be initialized with `this`,
    - Parameters need to be checked first, or
    - Exceptions need to be thrown.

---

## Scoped Enumerations
Unscoped enumerations simply use `enum`, while scoped enumerations use `enum class`.
- Unscoped enumerations cause some issues:
    1. They export their enumerators to the surrounding scope, meaning that name clashes can occur with other enumerators and
       it's impossible to use the fully-qualified name of the enumerator:
       ```c++
       enum Status { Unknown, Created, Connected };
       enum Codes { Unknown, Failure, OK }; // Error, name clash with enumerator "Unknown"
       auto status = Status::Created; // Error, cannot use fully-qualified unscoped enumerator
       ```
    2. Prior to C++11, the underlying integral type of the enumeration couldn't be specified. The value couldn't be larger
       than an `int`, unless the enumerator values couldn't fit a signed or unsigned `int`. Because the size of the underlying
       integral type was determined only when the enumeration was defined, forward declaration was not possible.
    3. Unscoped enumerators implicitly convert to integral values.
---

## Pointer to Implementation Idiom
Also called "opaque pointers," the Pimpl Idiom is a pattern that allows an interface's underlying data to change, without
having to recompile modules that rely on that interface.

`public.h`:
```c++
class PublicClass {
    public:
        void print() const;
        ...

    private:
        class Implementation; // Forward declaration of Implementation, but not defined in this header file
        unique_ptr< Implementation > opaque_pointer_;
};
```

`private.h`:
```c++
// Changes made to the implementation of Implementation will not cause a recompilation on TUs relying on PublicClass
#include "public.h"
class PublicClass::Implementation {
    Implementation();
    public:
        void print() const;
    private:
        int some_data_;
        string some_string_;
        // Data can be added without causing translation units relying on public.h to recompile
};
```

`public.cc`:
```c++
#include "public.h"
#include "private.h"

PublicClass::print()
{
    opaque_pointer_->print();
}

PublicClass::Implementation::print()
{
    cout << some_string_ << endl;
}
```

Changes made to `Implementation` will not cause a recompilation cascade to the TUs that rely on `public.h`. Changes made to
`public.cc` will not cause a recompilation cascade, unless changes were also made to `public.h`. However, recompilation will ensue
if:
- `PublicClass` changes - adding member data, changing function signatures, etc.
- Any headers that `PublicClass` includes that get changed will also cause `PublicClass` to change.

---

## Misc Features

### Linking Client C Code to a C++ Header
C++, unlike C, performs name mangling when compiling. This is because C++ allows function overloading while C does not. The
consequence of this is that symbol names in C++ will be mangled to include argument information, and this name is used to uniquely
identify this overloaded function during the linking stage. However, a C linker will be unable to find this function to link to,
because it doesn't understand name mangling. The solution to this problem is to use `extern "C"` when declaring something, which
will tell the C++ compiler to not perform name mangling on the function:
```c++
// Special syntax
extern "C" {
    void foo();
}
extern "C" void bar(); // Also valid syntax
```

This will allow a C linker to link with a C-compatible C++ header file.