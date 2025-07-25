---
title: "Checked Iterators"
description: "Learn more about: Checked Iterators"
ms.date: 11/04/2016
f1_keywords: ["_SECURE_SCL_THROWS"]
helpviewer_keywords: ["Safe Libraries", "Safe Libraries, C++ Standard Library", "Safe C++ Standard Library", "iterators, checked", "checked iterators"]
---
# Checked Iterators

Checked iterators ensure that the bounds of your container are not overwritten. Checked iterators apply to both release builds and debug builds. For more information about how to use debug iterators when you compile in debug mode, see [Debug Iterator Support](debug-iterator-support.md).

## Remarks

For information about how to disable warnings that are generated by checked iterators, see [`_SCL_SECURE_NO_WARNINGS`](scl-secure-no-warnings.md).

You can use the [`_ITERATOR_DEBUG_LEVEL`](iterator-debug-level.md) preprocessor macro to enable or disable the checked iterators feature. If `_ITERATOR_DEBUG_LEVEL` is defined as 1 or 2, unsafe use of iterators causes a runtime error and the program is terminated. If defined as 0, checked iterators are disabled. By default, the value for `_ITERATOR_DEBUG_LEVEL` is 0 for release builds and 2 for debug builds.

> [!IMPORTANT]
> Older documentation and source code may refer to the [`_SECURE_SCL`](secure-scl.md) macro. Use `_ITERATOR_DEBUG_LEVEL` to control `_SECURE_SCL`. For more information, see [`_ITERATOR_DEBUG_LEVEL`](iterator-debug-level.md).

When `_ITERATOR_DEBUG_LEVEL` is defined as 1 or 2, these iterator checks are performed:

- All standard iterators (for example, [`vector::iterator`](vector-class.md#iterator)) are checked.

- If an output iterator is a checked iterator, calls to standard library functions such as [`std::copy`](algorithm-functions.md#copy) get checked behavior.

- If an output iterator is an unchecked iterator, calls to standard library functions cause compiler warnings.

- The following functions generate a runtime error if there is an access that is outside the bounds of the container:

:::row:::
   :::column span="":::
      [`basic_string::operator[]`](basic-string-class.md#op_at)\
      [`bitset::operator[]`](bitset-class.md#op_at)\
      [`deque::back`](deque-class.md#back)\
      [`deque::front`](deque-class.md#front)
   :::column-end:::
   :::column span="":::
      [`deque::operator[]`](deque-class.md#op_at)\
      [`list::back`](list-class.md#back)\
      [`list::front`](list-class.md#front)\
      [`queue::back`](queue-class.md#back)
   :::column-end:::
   :::column span="":::
      [`queue::front`](queue-class.md#front)\
      [`vector::back`](vector-class.md#back)\
      [`vector::front`](vector-class.md#front)\
      [`vector::operator[]`](vector-class.md#op_at)
   :::column-end:::
:::row-end:::

When `_ITERATOR_DEBUG_LEVEL` is defined as 0:

- All standard iterators are unchecked. Iterators can move beyond the container boundaries, which leads to undefined behavior.

- If an output iterator is a checked iterator, calls to standard library functions such as `std::copy` get checked behavior.

- If an output iterator is an unchecked iterator, calls to standard library functions get unchecked behavior.

A checked iterator refers to an iterator that calls `invalid_parameter_handler` if you attempt to move past the boundaries of the container. For more information about `invalid_parameter_handler`, see [Parameter Validation](../c-runtime-library/parameter-validation.md).

The iterator adaptors that support checked iterators are [`checked_array_iterator` Class](checked-array-iterator-class.md) and [`unchecked_array_iterator` Class](unchecked-array-iterator-class.md).

## Examples

When you compile by using `_ITERATOR_DEBUG_LEVEL` set to 1 or 2, a runtime error will occur if you attempt to access an element that is outside the bounds of the container by using the indexing operator of certain classes.

```cpp
// checked_iterators_1.cpp
// cl.exe /Zi /MDd /EHsc /W4

#define _ITERATOR_DEBUG_LEVEL 1

#include <vector>
#include <iostream>

using namespace std;

int main()
{
    vector<int> v;
    v.push_back(67);

    int i = v[0];
    cout << i << endl;

    i = v[1]; //triggers invalid parameter handler
}
```

This program prints "67" then pops up an assertion failure dialog box with additional information about the failure.

Similarly, when you compile by using `_ITERATOR_DEBUG_LEVEL` set to 1 or 2, a runtime error will occur if you attempt to access an element by using `front` or `back` in container classes when the container is empty.

```cpp
// checked_iterators_2.cpp
// cl.exe /Zi /MDd /EHsc /W4
#define _ITERATOR_DEBUG_LEVEL 1

#include <vector>
#include <iostream>

using namespace std;

int main()
{
    vector<int> v;

    int& i = v.front(); // triggers invalid parameter handler
}
```

This program pops up an assertion failure dialog box with additional information about the failure.

The following code demonstrates various iterator use-case scenarios with comments about each. By default, `_ITERATOR_DEBUG_LEVEL` is set to 2 in Debug builds, and to 0 in Retail builds.

```cpp
// checked_iterators_3.cpp
// cl.exe /MTd /EHsc /W4

#include <algorithm>
#include <array>
#include <iostream>
#include <iterator>
#include <numeric>
#include <string>
#include <vector>

using namespace std;

template <typename C>
void print(const string& s, const C& c)
{
    cout << s;

    for (const auto& e : c)
    {
        cout << e << " ";
    }

    cout << endl;
}

int main()
{
    vector<int> v(16);
    iota(v.begin(), v.end(), 0);
    print("v: ", v);

    // OK: vector::iterator is checked in debug mode
    // (i.e. an overrun causes a debug assertion)
    vector<int> v2(16);
    transform(v.begin(), v.end(), v2.begin(), [](int n) { return n * 2; });
    print("v2: ", v2);

    // OK: back_insert_iterator is marked as checked in debug mode
    // (i.e. an overrun is impossible)
    vector<int> v3;
    transform(v.begin(), v.end(), back_inserter(v3), [](int n) { return n * 3; });
    print("v3: ", v3);

    // OK: array::iterator is checked in debug mode
    // (i.e. an overrun causes a debug assertion)
    array<int, 16> a4;
    transform(v.begin(), v.end(), a4.begin(), [](int n) { return n * 4; });
    print("a4: ", a4);

    // OK: Raw arrays are checked in debug mode
    // (an overrun causes a debug assertion)
    // NOTE: This applies only when raw arrays are given to C++ Standard Library algorithms!
    int a5[16];
    transform(v.begin(), v.end(), a5, [](int n) { return n * 5; });
    print("a5: ", a5);

    // WARNING C4996: Pointers cannot be checked in debug mode
    // (an overrun causes undefined behavior)
    int a6[16];
    int * p6 = a6;
    transform(v.begin(), v.end(), p6, [](int n) { return n * 6; });
    print("a6: ", a6);

    // OK: stdext::checked_array_iterator is checked in debug mode
    // (an overrun causes a debug assertion)
    int a7[16];
    int * p7 = a7;
    transform(v.begin(), v.end(), stdext::make_checked_array_iterator(p7, 16), [](int n) { return n * 7; });
    print("a7: ", a7);

    // WARNING SILENCED: stdext::unchecked_array_iterator is marked as checked in debug mode
    // (it performs no checking, so an overrun causes undefined behavior)
    int a8[16];
    int * p8 = a8;
    transform(v.begin(), v.end(), stdext::make_unchecked_array_iterator(p8), [](int n) { return n * 8; });
    print("a8: ", a8);
}
```

When you compile this code by using `cl.exe /EHsc /W4 /MTd checked_iterators_3.cpp` the compiler emits a warning, but compiles without error into an executable:

```Output
algorithm(1026) : warning C4996: 'std::_Transform1': Function call with parameters
that may be unsafe - this call relies on the caller to check that the passed values
are correct. To disable this warning, use -D_SCL_SECURE_NO_WARNINGS. See documentation
on how to use Visual C++ 'Checked Iterators'
```

When run at the command line, the executable generates this output:

```Output
v: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
v2: 0 2 4 6 8 10 12 14 16 18 20 22 24 26 28 30
v3: 0 3 6 9 12 15 18 21 24 27 30 33 36 39 42 45
a4: 0 4 8 12 16 20 24 28 32 36 40 44 48 52 56 60
a5: 0 5 10 15 20 25 30 35 40 45 50 55 60 65 70 75
a6: 0 6 12 18 24 30 36 42 48 54 60 66 72 78 84 90
a7: 0 7 14 21 28 35 42 49 56 63 70 77 84 91 98 105
a8: 0 8 16 24 32 40 48 56 64 72 80 88 96 104 112 120
```

## See also

[C++ Standard Library Overview](cpp-standard-library-overview.md)\
[Debug Iterator Support](debug-iterator-support.md)
