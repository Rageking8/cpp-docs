---
description: "Learn more about: C++ type system"
title: "C++ type system"
ms.date: 11/04/2022
ms.topic: "concept-article"
ms.assetid: 553c0ed6-77c4-43e9-87b1-c903eec53e80
---
# C++ type system

The concept of *type* is important in C++. Every variable, function argument, and function return value must have a type in order to be compiled. Also, all expressions (including literal values) are implicitly given a type by the compiler before they're evaluated. Some examples of types include built-in types such as **`int`** to store integer values, **`double`** to store floating-point values, or Standard Library types such as class [`std::basic_string`](../standard-library/basic-string-class.md) to store text. You can create your own type by defining a **`class`** or **`struct`**. The type specifies the amount of memory that's allocated for the variable (or expression result). The type also specifies the kinds of values that may be stored, how the compiler interprets the bit patterns in those values, and the operations you can perform on them. This article contains an informal overview of the major features of the C++ type system.

## Terminology

**Scalar type**: A type that holds a single value of a defined range. Scalars include arithmetic types (integral or floating-point values), enumeration type members, pointer types, pointer-to-member types, and `std::nullptr_t`. Fundamental types are typically scalar types.

**Compound type**: A type that isn't a scalar type. Compound types include array types, function types, class (or struct) types, union types, enumerations, references, and pointers to non-static class members.

**Variable**: The symbolic name of a quantity of data. The name can be used to access the data it refers to throughout the scope of the code where it's defined. In C++, *variable* is often used to refer to instances of scalar data types, whereas instances of other types are typically called *objects*.

**Object**: For simplicity and consistency, this article uses the term *object* to refer to any instance of a class or structure. When it's used in the general sense, it includes all types, even scalar variables.

**POD type** (plain old data): This informal category of data types in C++ refers to types that are scalar (see the Fundamental types section) or are *POD classes*. A POD class has no static data members that aren't also PODs, and has no user-defined constructors, user-defined destructors, or user-defined assignment operators. Also, a POD class has no virtual functions, no base class, and no private or protected non-static data members. POD types are often used for external data interchange, for example with a module written in the C language (which has POD types only).

## Specifying variable and function types

C++ is both a *strongly typed* language and a *statically typed* language; every object has a type and that type never changes. When you declare a variable in your code, you must either specify its type explicitly, or use the **`auto`** keyword to instruct the compiler to deduce the type from the initializer. When you declare a function in your code, you must specify the type of its return value and of each argument. Use the return value type **`void`** if no value is returned by the function. The exception is when you're using function templates, which allow for arguments of arbitrary types.

After you first declare a variable, you can't change its type at some later point. However, you can copy the variable's value or a function's return value into another variable of a different type. Such operations are called *type conversions*, which are sometimes necessary but are also potential sources of data loss or incorrectness.

When you declare a variable of POD type, we strongly recommend you *initialize* it, which means to give it an initial value. Until you initialize a variable, it has a "garbage" value that consists of whatever bits happened to be in that memory location previously. It's an important aspect of C++ to remember, especially if you're coming from another language that handles initialization for you. When you declare a variable of non-POD class type, the constructor handles initialization.

The following example shows some simple variable declarations with some descriptions for each. The example also shows how the compiler uses type information to allow or disallow certain subsequent operations on the variable.

```cpp
int result = 0;              // Declare and initialize an integer.
double coefficient = 10.8;   // Declare and initialize a floating
                             // point value.
auto name = "Lady G.";       // Declare a variable and let compiler
                             // deduce the type.
auto address;                // error. Compiler cannot deduce a type
                             // without an intializing value.
age = 12;                    // error. Variable declaration must
                             // specify a type or use auto!
result = "Kenny G.";         // error. Can't assign text to an int.
string result = "zero";      // error. Can't redefine a variable with
                             // new type.
int maxValue;                // Not recommended! maxValue contains
                             // garbage bits until it is initialized.
```

## Fundamental (built-in) types

Unlike some languages, C++ has no universal base type from which all other types are derived. The language includes many *fundamental types*, also known as *built-in types*. These types include numeric types such as **`int`**, **`double`**, **`long`**, **`bool`**, plus the **`char`** and **`wchar_t`** types for ASCII and UNICODE characters, respectively. Most integral fundamental types (except **`bool`**, **`double`**, **`wchar_t`**, and related types) all have **`unsigned`** versions, which modify the range of values that the variable can store. For example, an **`int`**, which stores a 32-bit signed integer, can represent a value from -2,147,483,648 to 2,147,483,647. An **`unsigned int`**, which is also stored as 32 bits, can store a value from 0 to 4,294,967,295. The total number of possible values in each case is the same; only the range is different.

The compiler recognizes these built-in types, and it has built-in rules that govern what operations you can perform on them, and how they can be converted to other fundamental types. For a complete list of built-in types and their size and numeric limits, see [Built-in types](../cpp/fundamental-types-cpp.md).

The following illustration shows the relative sizes of the built-in types in the Microsoft C++ implementation:

![Diagram of the relative size in bytes of several built in types.](../cpp/media/built-intypesizes.png)

The following table lists the most frequently used fundamental types, and their sizes in the Microsoft C++ implementation:

| Type | Size | Comment |
|--|--|--|
| **`int`** | 4 bytes | The default choice for integral values. |
| **`double`** | 8 bytes | The default choice for floating point values. |
| **`bool`** | 1 byte | Represents values that can be either true or false. |
| **`char`** | 1 byte | Use for ASCII characters in older C-style strings or std::string objects that will never have to be converted to UNICODE. |
| **`wchar_t`** | 2 bytes | Represents "wide" character values that may be encoded in UNICODE format (UTF-16 on Windows, other operating systems may differ). **`wchar_t`** is the character type that's used in strings of type `std::wstring`. |
| **`unsigned char`** | 1 byte | C++ has no built-in byte type.  Use **`unsigned char`** to represent a byte value. |
| **`unsigned int`** | 4 bytes | Default choice for bit flags. |
| **`long long`** | 8 bytes | Represents a much larger range of integer values. |

Other C++ implementations may use different sizes for certain numeric types. For more information on the sizes and size relationships that the C++ standard requires, see [Built-in types](fundamental-types-cpp.md).

## The `void` type

The **`void`** type is a special type; you can't declare a variable of type **`void`**, but you can declare a variable of type `void *` (pointer to **`void`**), which is sometimes necessary when allocating raw (untyped) memory. However, pointers to **`void`** aren't type-safe and their use is discouraged in modern C++. In a function declaration, a **`void`** return value means that the function doesn't return a value; using it as a return type is a common and acceptable use of **`void`**. While the C language required functions that have zero parameters to declare **`void`** in the parameter list, for example, `fn(void)`, this practice is discouraged in modern C++; a parameterless function should be declared `fn()`. For more information, see [Type conversions and type safety](../cpp/type-conversions-and-type-safety-modern-cpp.md).

## `const` type qualifier

Any built-in or user-defined type may be qualified by the **`const`** keyword. Additionally, member functions may be **`const`**-qualified and even **`const`**-overloaded. The value of a **`const`** type can't be modified after it's initialized.

```cpp
const double PI = 3.1415;
PI = .75; //Error. Cannot modify const variable.
```

The **`const`** qualifier is used extensively in function and variable declarations and "const correctness" is an important concept in C++; essentially it means to use **`const`** to guarantee, at compile time, that values aren't modified unintentionally. For more information, see [`const`](../cpp/const-cpp.md).

A **`const`** type is distinct from its non-**`const`** version; for example, **`const int`** is a distinct type from **`int`**. You can use the C++ **`const_cast`** operator on those rare occasions when you must remove *const-ness* from a variable. For more information, see [Type conversions and type safety](../cpp/type-conversions-and-type-safety-modern-cpp.md).

## String types

Strictly speaking, the C++ language has no built-in string type; **`char`** and **`wchar_t`** store single characters - you must declare an array of these types to approximate a string, adding a terminating null value (for example, ASCII `'\0'`) to the array element one past the last valid character (also called a *C-style string*). C-style strings required much more code to be written or the use of external string utility library functions. But in modern C++, we have the Standard Library types `std::string` (for 8-bit **`char`**-type character strings) or `std::wstring` (for 16-bit **`wchar_t`**-type character strings). These C++ Standard Library containers can be thought of as native string types because they're part of the standard libraries that are included in any conformant C++ build environment. Use the `#include <string>` directive to make these types available in your program. (If you're using MFC or ATL, the `CString` class is also available, but isn't part of the C++ standard.) The use of null-terminated character arrays (the C-style strings previously mentioned) is discouraged in modern C++.

## User-defined types

When you define a **`class`**, **`struct`**, **`union`**, or **`enum`**, that construct is used in the rest of your code as if it were a fundamental type. It has a known size in memory, and certain rules about how it can be used apply to it for compile-time checking and, at runtime, for the life of your program. The primary differences between the fundamental built-in types and user-defined types are as follows:

- The compiler has no built-in knowledge of a user-defined type. It learns of the type when it first encounters the definition during the compilation process.

- You specify what operations can be performed on your type, and how it can be converted to other types, by defining (through overloading) the appropriate operators, either as class members or non-member functions. For more information, see [Function overloading](function-overloading.md)

## Pointer types

As in the earliest versions of the C language, C++ continues to let you declare a variable of a pointer type by using the special declarator **`*`** (asterisk). A pointer type stores the address of the location in memory where the actual data value is stored. In modern C++, these pointer types are referred to as *raw pointers*, and they're accessed in your code through special operators: **`*`** (asterisk) or **`->`** (dash with greater-than, often called *arrow*). This memory access operation is called *dereferencing*. Which operator you use depends on whether you're dereferencing a pointer to a scalar, or a pointer to a member in an object.

Working with pointer types has long been one of the most challenging and confusing aspects of C and C++ program development. This section outlines some facts and practices to help use raw pointers if you want to. However, in modern C++, it's no longer required (or recommended) to use raw pointers for object ownership at all, due to the evolution of the [smart pointer](../cpp/smart-pointers-modern-cpp.md) (discussed more at the end of this section). It's still useful and safe to use raw pointers for observing objects. However, if you must use them for object ownership, you should do so with caution and with careful consideration of how the objects owned by them are created and destroyed.

The first thing that you should know is that a raw pointer variable declaration only allocates enough memory to store an address: the memory location that the pointer refers to when it's dereferenced. The pointer declaration doesn't allocate the memory needed to store the data value. (That memory is also called the *backing store*.) In other words, by declaring a raw pointer variable, you're creating a memory address variable, not an actual data variable. If you dereference a pointer variable before you've made sure that it contains a valid address for a backing store, it causes undefined behavior (usually a fatal error) in your program. The following example demonstrates this kind of error:

```cpp
int* pNumber;       // Declare a pointer-to-int variable.
*pNumber = 10;      // error. Although this may compile, it is
                    // a serious error. We are dereferencing an
                    // uninitialized pointer variable with no
                    // allocated memory to point to.
```

The example dereferences a pointer type without having any memory allocated to store the actual integer data or a valid memory address assigned to it. The following code corrects these errors:

```cpp
    int number = 10;          // Declare and initialize a local integer
                              // variable for data backing store.
    int* pNumber = &number;   // Declare and initialize a local integer
                              // pointer variable to a valid memory
                              // address to that backing store.
...
    *pNumber = 41;            // Dereference and store a new value in
                              // the memory pointed to by
                              // pNumber, the integer variable called
                              // "number". Note "number" was changed, not
                              // "pNumber".
```

The corrected code example uses local stack memory to create the backing store that `pNumber` points to. We use a fundamental type for simplicity. In practice, the backing stores for pointers are most often user-defined types that are dynamically allocated in an area of memory called the *heap* (or *free store*) by using a **`new`** keyword expression (in C-style programming, the older `malloc()` C runtime library function was used). Once allocated, these variables are normally referred to as *objects*, especially if they're based on a class definition. Memory that is allocated with **`new`** must be deleted by a corresponding **`delete`** statement (or, if you used the `malloc()` function to allocate it, the C runtime function `free()`).

However, it's easy to forget to delete a dynamically allocated object- especially in complex code, which causes a resource bug called a *memory leak*. For this reason, the use of raw pointers is discouraged in modern C++. It's almost always better to wrap a raw pointer in a [smart pointer](../cpp/smart-pointers-modern-cpp.md), which automatically releases the memory when its destructor is invoked. (That is, when the code goes out of scope for the smart pointer.) By using smart pointers, you virtually eliminate a whole class of bugs in your C++ programs. In the following example, assume `MyClass` is a user-defined type that has a public method `DoSomeWork();`

```cpp
void someFunction() {
    unique_ptr<MyClass> pMc(new MyClass);
    pMc->DoSomeWork();
}
  // No memory leak. Out-of-scope automatically calls the destructor
  // for the unique_ptr, freeing the resource.
```

For more information about smart pointers, see [Smart pointers](../cpp/smart-pointers-modern-cpp.md).

For more information about pointer conversions, see [Type conversions and type safety](../cpp/type-conversions-and-type-safety-modern-cpp.md).

For more information about pointers in general, see [Pointers](../cpp/pointers-cpp.md).

## Windows data types

In classic Win32 programming for C and C++, most functions use Windows-specific typedefs and `#define` macros (defined in `windef.h`) to specify the types of parameters and return values. These Windows data types are mostly special names (aliases) given to C/C++ built-in types. For a complete list of these typedefs and preprocessor definitions, see [Windows Data Types](/windows/win32/WinProg/windows-data-types). Some of these typedefs, such as `HRESULT` and `LCID`, are useful and descriptive. Others, such as `INT`, have no special meaning and are just aliases for fundamental C++ types. Other Windows data types have names that are retained from the days of C programming and 16-bit processors, and have no purpose or meaning on modern hardware or operating systems. There are also special data types associated with the Windows Runtime Library, listed as [Windows Runtime base data types](/windows/win32/WinRT/base-data-types). In modern C++, the general guideline is to prefer the C++ fundamental types unless the Windows type communicates some extra meaning about how the value is to be interpreted.

## More information

For more information about the C++ type system, see the following articles.

[Value types](../cpp/value-types-modern-cpp.md)\
Describes *value types* along with issues relating to their use.

[Type conversions and type safety](../cpp/type-conversions-and-type-safety-modern-cpp.md)\
Describes common type conversion issues and shows how to avoid them.

## See also

[Welcome back to C++](../cpp/welcome-back-to-cpp-modern-cpp.md)\
[C++ language reference](../cpp/cpp-language-reference.md)\
[C++ Standard Library](../standard-library/cpp-standard-library-reference.md)
