---
title: "Microsoft C/C++ compiler (MSVC) warnings C4400 through C4599"
description: "Table of Microsoft C/C++ compiler (MSVC) warnings C4400 through C4599"
ms.date: "1/22/2025"
f1_keywords: ["C4413", "C4415", "C4416", "C4417", "C4418", "C4419", "C4421", "C4423", "C4424", "C4425", "C4426", "C4427", "C4438", "C4442", "C4443", "C4444", "C4446", "C4447", "C4448", "C4449", "C4450", "C4451", "C4452", "C4453", "C4454", "C4455", "C4466", "C4467", "C4468", "C4472", "C4474", "C4475", "C4476", "C4478", "C4480", "C4482", "C4483", "C4491", "C4492", "C4493", "C4494", "C4495", "C4496", "C4497", "C4498", "C4499", "C4509", "C4519", "C4531", "C4542", "C4562", "C4568", "C4569", "C4573", "C4574", "C4575", "C4576", "C4578", "C4582", "C4583", "C4585", "C4586", "C4587", "C4588", "C4589", "C4591", "C4592", "C4593", "C4594", "C4595", "C4598", "C4599"]
helpviewer_keywords: ["C4413", "C4415", "C4416", "C4417", "C4418", "C4419", "C4421", "C4423", "C4424", "C4425", "C4426", "C4427", "C4438", "C4442", "C4443", "C4444", "C4446", "C4447", "C4448", "C4449", "C4450", "C4451", "C4452", "C4453", "C4454", "C4455", "C4466", "C4467", "C4468", "C4472", "C4474", "C4475", "C4476", "C4478", "C4480", "C4482", "C4483", "C4491", "C4492", "C4493", "C4494", "C4495", "C4496", "C4497", "C4498", "C4499", "C4509", "C4519", "C4531", "C4542", "C4562", "C4568", "C4569", "C4573", "C4574", "C4575", "C4576", "C4578", "C4582", "C4583", "C4585", "C4586", "C4587", "C4588", "C4589", "C4591", "C4592", "C4593", "C4594", "C4595", "C4598", "C4599"]
---
# Microsoft C/C++ compiler warnings C4400 through C4599

The articles in this section describe Microsoft C/C++ compiler warning messages C4400-C4599.

[!INCLUDE[error-boilerplate](../../error-messages/includes/error-boilerplate.md)]

## Warning messages

|Warning|Message|
|-------------|-------------|
|[Compiler warning (level 4, Error) C4400](compiler-warning-level-4-c4400.md)|'*type*': `const`/`volatile` qualifiers on this type are not supported|
|[Compiler warning (level 1) C4401](compiler-warning-level-1-c4401.md)|'*bitfield*': member is bit field|
|[Compiler warning (level 1) C4402](compiler-warning-level-1-c4402.md)|must use `PTR` operator|
|[Compiler warning (level 1) C4403](compiler-warning-level-1-c4403.md)|illegal `PTR` operator|
|[Compiler warning (level 3) C4404](compiler-warning-level-3-c4404.md)|period on directive ignored|
|[Compiler warning (level 1) C4405](compiler-warning-level-1-c4405.md)|'*identifier*': identifier is reserved word|
|[Compiler warning (level 1) C4406](compiler-warning-level-1-c4406.md)|operand on directive ignored|
|[Compiler warning (level 1) C4407](compiler-warning-level-1-c4407.md)|cast between different pointer to member representations, compiler may generate incorrect code|
|[Compiler warning (level 4) C4408](compiler-warning-level-4-c4408.md)|anonymous *struct/union* did not declare any data members|
|[Compiler warning (level 1) C4409](compiler-warning-level-1-c4409.md)|illegal instruction size|
|[Compiler warning (level 1) C4410](compiler-warning-level-1-c4410.md)|illegal size for operand|
|[Compiler warning (level 1) C4411](compiler-warning-level-1-c4411.md)|'*identifier*': symbol resolves to displacement register|
|[Compiler warning (level 2, off) C4412](compiler-warning-level-2-c4412.md)|'*function*': function signature contains type '*type*'; C++ objects are unsafe to pass between pure code and mixed or native.|
|Compiler warning (no longer emitted) C4413|'`classname::member`': reference member is initialized to a temporary that doesn't persist after the constructor exits|
|[Compiler warning (level 3) C4414](compiler-warning-level-3-c4414.md)|'*function*': short jump to function converted to near|
|Compiler warning (level 1) C4415|duplicate `__declspec(code_seg(`'*name*'`))`|
|Compiler warning (level 1) C4416|`__declspec(code_seg(...))` contains empty string: ignored|
|Compiler warning (level 1) C4417|an explicit template instantiation cannot have `__declspec(code_seg(...))`: ignored|
|Compiler warning (level 1) C4418|`__declspec(code_seg(...))` ignored on an `enum`|
|Compiler warning (level 3) C4419|'*symbol*' has no effect when applied to `private ref class` '*class*'.|
|[Compiler warning (level 1) C4420](compiler-warning-level-1-c4420.md)|'*checked_operator*': operator not available, using '*operator*' instead; run-time checking may be compromised|
|Compiler warning (level 3) C4421|'*parameter*': a reference parameter on a resumable function is potentially unsafe|
|Compiler warning (level 3) C4423|'`std::bad_alloc`': will be caught by class ('*type*') on line *number*|
|Compiler warning (level 3) C4424|catch for '*type1*' preceded by '*type2*' on line *number*; unpredictable behavior may result if '`std::bad_alloc`' is thrown|
|Compiler warning (level 1) C4425|A SAL annotation cannot be applied to '`...`'|
|Compiler warning (level 1, off) C4426|optimization flags changed after including header, may be due to `#pragma optimize()`|
|Compiler warning (level 1) C4427|'*operator*': overflow in constant division, undefined behavior|
|[Compiler warning (level 4) C4429](compiler-warning-level-4-c4429.md)|possible incomplete or improperly formed universal-character-name|
|[Compiler warning (level 1, Error) C4430](compiler-warning-c4430.md)|missing type specifier - int assumed. Note: C++ does not support default-int|
|[Compiler warning (level 4) C4431](compiler-warning-level-4-c4431.md)|missing type specifier - int assumed. Note: C no longer supports default-int|
|[Compiler warning (level 4) C4434](compiler-warning-level-4-c4434.md)|a static constructor must have private accessibility; changing to private access|
|[Compiler warning (level 4, off) C4435](compiler-warning-level-4-c4435.md)|'*derived_class*': Object layout under `/vd2` will change due to virtual base '*base_class*'|
|[Compiler warning (level 1 and level 4) C4436](compiler-warning-level-1-c4436.md)|`dynamic_cast` from virtual base '*base_class*' to '*derived_class*' in constructor or destructor could fail with partially-constructed object|
|[Compiler warning (level 1 and level 4, off) C4437](compiler-warning-level-4-c4437.md)|`dynamic_cast` from virtual base '*base_class*' to '*derived_class*' could fail in some contexts|
|Compiler warning C4438|'*function*': cannot be called safely in `/await:clrcompat` mode. If '*function*' calls into the CLR it may result in CLR head corruption|
|[Compiler warning (level 1, Error) C4439](compiler-warning-c4439.md)|'*function*': function definition with a managed type in the signature must have a `__clrcall` calling convention|
|[Compiler warning (level 1) C4440](compiler-warning-level-1-c4440.md)|calling convention redefinition from '*calling_convention1*' to '*calling_convenction2*' ignored|
|[Compiler warning (level 1) C4441](compiler-warning-level-1-c4441.md)|calling convention of '*calling_convention1*' ignored; '*calling_convention2*' used instead|
|Compiler warning (level 1) C4442|embedded null terminator in `__annotation` argument.  Value will be truncated.|
|Compiler warning (level 1) C4443|expected pragma parameter to be '`0`', '`1`', or '`2`'|
|Compiler warning (level 3, off) C4444|'*identifier*': top level '`__unaligned`' is not implemented in this context|
|[Compiler warning (level 1) C4445](compiler-warning-level-1-c4445.md)|'*function*': in a *WinRT/managed* type a virtual method cannot be private|
|Compiler warning (level 1) C4446|'*type*': cannot map member '*name1*' into this type, due to conflict with the type name. The method was renamed to '*name2*'|
|Compiler warning (level 1) C4447|'`main`' signature found without threading model. Consider using '`int main(Platform::Array<Platform::String^>^ args)`'.|
|Compiler warning (level 1) C4448|'*type1*' does not have a default interface specified in metadata. Picking: '*type2*', which may fail at runtime.|
|Compiler warning C4449|'*type*' an unsealed type should be marked as '`[WebHostHidden]`'|
|Compiler warning C4450|'*type1*' should be marked as '`[WebHostHidden]`' because it derives from '*type2*'|
|Compiler warning (level 3 and level 4) C4451|'*classname1::member*': Usage of ref class '*classname2::member*' inside this context can lead to invalid marshaling of object across contexts|
|Compiler warning (level 1, Error) C4452|'*identifier*': public type cannot be at global scope. It must be in a namespace that is a child of the name of the output `.winmd` file.|
|Compiler warning (level 1) C4453|'*type*': A '`[WebHostHidden]`' type should not be used on the published surface of a public type that is not '`[WebHostHidden]`'|
|Compiler warning (level 1) C4454|'*function*' is overloaded by more than the number of input parameters without having `[DefaultOverload]` specified. Picking '*declaration*' as the default overload|
|Compiler warning (level 1) C4455|'operator *operator*': literal suffix identifiers that do not start with an underscore are reserved|
|[Compiler warning (level 1 and level 4) C4456](compiler-warning-level-4-c4456.md)|declaration of '*identifier*' hides previous local declaration|
|[Compiler warning (level 1 and level 4) C4457](compiler-warning-level-4-c4457.md)|declaration of '*identifier*' hides function parameter|
|[Compiler warning (level 1 and level 4) C4458](compiler-warning-level-4-c4458.md)|declaration of '*identifier*' hides class member|
|[Compiler warning (level 1 and level 4) C4459](compiler-warning-level-4-c4459.md)|declaration of '*identifier*' hides global declaration|
|[Compiler warning (level 4) C4460](compiler-warning-level-4-c4460.md)|*WinRT/managed* operator '*operator*', has parameter passed by reference. *WinRT/managed* operator '*operator*' has different semantics from C++ operator '*cpp_operator*', did you intend to pass by value?|
|[Compiler warning (level 1) C4461](compiler-warning-level-1-c4461.md)|'*classname*': this class has a finalizer '`!`*finalizer*' but no destructor '`~`*dtor*'|
|[Compiler warning (level 1, Error) C4462](compiler-warning-level-1-c4462.md)|'*type*' : cannot determine the GUID of the type. Program may fail at runtime.|
|[Compiler warning (level 4) C4463](compiler-warning-level-4-c4463.md)|overflow; assigning *value* to bit-field that can only hold values from *min_value* to *max_value*|
|[Compiler warning (level 4, off) C4464](compiler-warning-level-4-c4464.md)|relative include path contains '`..`'|
|Compiler warning C4466|Could not perform coroutine heap elision|
|Compiler warning (level 1) C4465|'*identifier*': use of dependent template requires `::template`|
|Compiler warning (level 1) C4467|Usage of ATL attributes is deprecated|
|Compiler warning (level 1) C4468|The `[[fallthrough]]` attribute must be followed by a `case` label or a `default` label|
|[Compiler warning (level 1) C4470](compiler-warning-level-1-c4470.md)|floating-point control pragmas ignored under `/clr`|
|[Compiler warning (level 4, off) C4471](compiler-warning-level-4-c4471.md)|'*enumeration*': a forward declaration of an unscoped enumeration must have an underlying type|
|Compiler warning (level 1) C4472|'*identifier*' is a native enum: add an access specifier (private/public) to declare a 'WinRT/managed' enum|
|[Compiler warning (level 1) C4473](c4473.md)|'*function*' : not enough arguments passed for format string|
|Compiler warning (level 3) C4474|'*function*' : too many arguments passed for format string|
|Compiler warning (level 3) C4475|'*function*' : length modifier '*modifier*' cannot be used with type field character '*character*' in format specifier|
|Compiler warning (level 3) C4476|'*function*' : unknown type field character '*character*' in format specifier|
|[Compiler warning (level 1) C4477](c4477.md)|'*function*' : format string '*string*' requires an argument of type '*type*', but variadic argument *number* has type '*type*'|
|Compiler warning (level 1) C4478|'*function*' : positional and non-positional placeholders cannot be mixed in the same format string|
|Compiler warning (Error) C4480|nonstandard extension used: specifying underlying type for `enum` '*enumeration*'|
|[Compiler warning (level 4, Error) C4481](compiler-warning-level-4-c4481.md)|nonstandard extension used: override specifier '*keyword*'|
|Compiler warning C4482|nonstandard extension used: `enum` '*enumeration*' used in qualified name|
|Compiler warning (level 1, Error) C4483|syntax error: expected C++ keyword|
|[Compiler warning (level 1, Error) C4484](compiler-warning-c4484.md)|'*override_function*': matches base `ref class` method '*base_class_function*', but is not marked '`virtual`', '`new`' or '`override`'; '`new`' (and not '`virtual`') is assumed|
|[Compiler warning (level 1, Error) C4485](compiler-warning-c4485.md)|'*override_function*': matches base `ref class` method '*base_class_function*', but is not marked '`new`' or '`override`'; '`new`' (and '`virtual`') is assumed|
|[Compiler warning (level 1) C4486](compiler-warning-level-1-c4486.md)|'*function*': a private virtual method of a `ref class` or value class should be marked '`sealed`'|
|[Compiler warning (level 4) C4487](compiler-warning-level-4-c4487.md)|'*derived_class_function*': matches inherited non-virtual method '*base_class_function*' but is not explicitly marked '`new`'|
|[Compiler warning (level 1) C4488](compiler-warning-level-1-c4488.md)|'*function*': requires '*keyword*' keyword to implement the interface method '*interface_method*'|
|[Compiler warning (level 1) C4489](compiler-warning-level-1-c4489.md)|'*specifier*': not allowed on interface method '*method*'; override specifiers are only allowed on ref class and value class methods|
|[Compiler warning (level 1) C4490](compiler-warning-level-1-c4490.md)|'*override*': incorrect use of override specifier; '*function*' does not match a base ref class method|
|Compiler warning (level 1) C4491|'*name*': has an illegal IDL version format|
|Compiler warning (level 1, Error) C4492|'*function1*': matches base `ref class` method '*function2*', but is not marked '`override`'|
|Compiler warning (level 3, Error) C4493|delete expression has no effect as the destructor of '*type*' does not have '`public`' accessibility|
|Compiler warning (level 1) C4494|'*function*' : Ignoring `__declspec(allocator)` because the function return type is not a pointer or reference|
|Compiler warning (level 4, off) C4495|nonstandard extension '`__super`' used: replace with explicit base class name|
|Compiler warning (level 4, Error, off) C4496|nonstandard extension '`for each`' used: replace with ranged-for statement|
|Compiler warning (level 4, off) C4497|nonstandard extension '`sealed`' used: replace with '`final`'|
|Compiler warning (level 4, off) C4498|nonstandard extension used: '*extension*'|
|Compiler warning (level 4) C4499|'*function*': an explicit specialization cannot have a storage class (ignored)|
|[Compiler warning (level 1) C4502](compiler-warning-level-1-c4502.md)|'linkage specification' requires use of keyword '`extern`' and must precede all other specifiers|
|[Compiler warning (level 1) C4503](compiler-warning-level-1-c4503.md)|'*identifier*': decorated name length exceeded, name was truncated|
|[Compiler warning (level 4) C4505](compiler-warning-level-4-c4505.md)|'*function*': unreferenced function with internal linkage has been removed|
|[Compiler warning (level 1) C4506](compiler-warning-level-1-c4506.md)|no definition for inline function '*function*'|
|[Compiler warning (level 1) C4508](compiler-warning-level-1-c4508.md)|'*function*': function should return a value; '`void`' return type assumed|
|Compiler warning C4509|nonstandard extension used: '*function*' uses SEH and '*object*' has destructor|
|[Compiler warning (level 4) C4510](compiler-warning-level-4-c4510.md)|'*class*': default constructor was implicitly defined as deleted|
|[Compiler warning (level 4) C4511](compiler-warning-level-3-c4511.md)|'*class*': copy constructor was implicitly defined as deleted|
|[Compiler warning (level 4) C4512](compiler-warning-level-4-c4512.md)|'*class*': assignment operator was implicitly defined as deleted|
|[Compiler warning (level 4) C4513](compiler-warning-level-4-c4513.md)|'*class*': destructor was implicitly defined as deleted|
|[Compiler warning (level 4, off) C4514](compiler-warning-level-4-c4514.md)|'*function*': unreferenced inline function has been removed|
|[Compiler warning (level 4) C4515](compiler-warning-level-4-c4515.md)|'*namespace*': namespace uses itself|
|[Compiler warning (level 4) C4516](compiler-warning-level-4-c4516.md)|'*class*::*symbol*': access-declarations are deprecated; member using-declarations provide a better alternative|
|[Compiler warning (level 4) C4517](compiler-warning-level-4-c4517.md)|access-declarations are deprecated; member using-declarations provide a better alternative|
|[Compiler warning (level 1) C4518](compiler-warning-level-1-c4518.md)|'*specifier*': storage-class or type specifier(s) unexpected here; ignored|
|Compiler warning (level1, Error, no longer emitted) C4519|default template arguments are only allowed on a class template|
|[Compiler warning (level 3) C4521](compiler-warning-level-3-c4521.md)|'*class*': multiple copy constructors specified|
|[Compiler warning (level 3) C4522](compiler-warning-level-3-c4522.md)|'*class*': multiple assignment operators specified|
|[Compiler warning (level 3) C4523](compiler-warning-level-3-c4523.md)|'*class*': multiple destructors specified|
|[Compiler warning (level 1) C4526](compiler-warning-level-1-c4526.md)|'*function*': static member function cannot override virtual function '*virtual function*'<br/>override ignored, virtual function will be hidden|
|[Compiler warning (level 1) C4530](compiler-warning-level-1-c4530.md)|C++ exception handler used, but unwind semantics are not enabled. Specify `/EHsc`|
|Compiler warning (level 1) C4531|C++ exception handling not available on Windows CE. Use Structured Exception Handling|
|[Compiler warning (level 1) C4532](compiler-warning-level-1-c4532.md)|'*continue*': jump out of *__finally/finally* block has undefined behavior during termination handling|
|[Compiler warning (level 1) C4533](compiler-warning-level-1-c4533.md)|initialization of '*variable*' is skipped by '`goto` *label*'|
|[Compiler warning (level 3) C4534](compiler-warning-level-3-c4534.md)|'*constructor*' will not be a default constructor for *class/struct* '*identifier*' due to the default argument|
|[Compiler warning (level 3) C4535](compiler-warning-level-3-c4535.md)|calling `_set_se_translator()` requires `/EHa`|
|[Compiler warning (level 4, off) C4536](compiler-warning-level-4-c4536.md)|'*typename*': type-name exceeds meta-data limit of '*character_limit*' characters|
|[Compiler warning (level 1) C4537](compiler-warning-level-1-c4537.md)|'*object*': '`.`' applied to non-UDT type|
|[Compiler warning (level 3) C4538](compiler-warning-level-3-c4538.md)|'*type*': `const`/`volatile` qualifiers on this type are not supported|
|[Compiler warning (level 1) C4540](compiler-warning-level-1-c4540.md)|`dynamic_cast` used to convert to inaccessible or ambiguous base; run-time test will fail ('*type1*' to '*type2*')|
|[Compiler warning (level 1) C4541](compiler-warning-level-1-c4541.md)|'*identifier*' used on polymorphic type '*type*' with `/GR-`; unpredictable behavior may result|
|Compiler warning (level 1) C4542|Skipping generation of merged injected text file, cannot write *filetype* file: '*issue*': *message*|
|[Compiler warning (level 3) C4543](compiler-warning-level-3-c4543.md)|Injected text suppressed by attribute '`no_injected_text`'|
|[Compiler warning (level 1) C4544](compiler-warning-level-1-c4544.md)|the default template argument for '*declaration*' is ignored on this template declaration|
|[Compiler warning (level 1, off) C4545](compiler-warning-level-1-c4545.md)|expression before comma evaluates to a function which is missing an argument list|
|[Compiler warning (level 1, off) C4546](compiler-warning-level-1-c4546.md)|function call before comma missing argument list|
|[Compiler warning (level 1, off) C4547](compiler-warning-level-1-c4547.md)|'*operator*': operator before comma has no effect; expected operator with side-effect|
|[Compiler warning (level 1, off) C4548](compiler-warning-level-1-c4548.md)|expression before comma has no effect; expected expression with side-effect|
|[Compiler warning (level 1, off) C4549](compiler-warning-level-1-c4549.md)|'*operator*': operator before comma has no effect; did you intend '*operator*'?|
|[Compiler warning (level 1) C4550](compiler-warning-level-1-c4550.md)|expression evaluates to a function which is missing an argument list|
|[Compiler warning (level 1) C4551](compiler-warning-level-1-c4551.md)|function call missing argument list|
|[Compiler warning (level 1) C4552](compiler-warning-level-1-c4552.md)|'*operator*': result of expression not used|
|[Compiler warning (level 1) C4553](compiler-warning-level-1-c4553.md)|'*operator*': result of expression not used; did you intend '*operator*'?|
|[Compiler warning (level 3) C4554](compiler-warning-level-3-c4554.md) C4554|'*operator*': check operator precedence for possible error; use parentheses to clarify precedence|
|[Compiler warning (level 1, off) C4555](compiler-warning-level-1-c4555.md)|result of expression not used|
|[Compiler warning (level 1) C4556](compiler-warning-level-1-c4556.md)|value of intrinsic immediate argument '*value*' is out of range '*lower_bound* - *upper_bound*'|
|[Compiler warning (level 3, off) C4557](compiler-warning-level-3-c4557.md)|'`__assume`' contains effect '*effect*'|
|[Compiler warning (level 1) C4558](compiler-warning-level-1-c4558.md)|value of operand '*value*' is out of range '*lower_bound* - *upper_bound*'|
|[Compiler warning (level 4) C4559](compiler-warning-level-4-c4559.md)|'*function*': redefinition; the function gains `__declspec(`*modifier*`)`|
|[Compiler warning (level 1) C4561](compiler-warning-level-1-c4561.md)|'`__fastcall`' incompatible with the '`/clr`' option: converting to '`__stdcall`'|
|Compiler warning (level 4) C4562|fully prototyped functions are required with the '`/clr`' option: converting '`()`' to '`(void)`'|
|[Compiler warning (level 4) C4564](compiler-warning-level-4-c4564.md)|method '*method*' of *class* '*classname*' defines unsupported default parameter '*parameter*'|
|[Compiler warning (level 4) C4565](compiler-warning-level-4-c4565.md)|'*function*': redefinition; the symbol was previously declared with `__declspec(`*modifier*`)`|
|[Compiler warning (level 1) C4566](compiler-warning-level-1-c4566.md)|character represented by universal-character-name '*char*' cannot be represented in the current code page (*number*)|
|Compiler warning (level 1) C4568|'*function*': no members match the signature of the explicit override|
|Compiler warning (level 3) C4569|'*function*': no members match the signature of the explicit override|
|[Compiler warning (level 3) C4570](compiler-warning-level-3-c4570.md)|'*type*': is not explicitly declared as abstract but has abstract functions|
|[Compiler warning (level 4) C4571](compiler-warning-level-4-c4571.md)|Informational: `catch(...)` semantics changed since Visual C++ 7.1; structured exceptions (SEH) are no longer caught|
|[Compiler warning (level 1) C4572](compiler-warning-level-1-c4572.md)|`[ParamArray]` attribute is deprecated under `/clr`, use '`...`' instead|
|Compiler warning (level 1, Error) C4573|the usage of '*lambda function*' requires the compiler to capture '`this`' but the current default capture mode does not allow it|
|Compiler warning (level 4, off) C4574|'*Identifier*' is defined to be '`0`': did you mean to use '`#if` *identifier*'?|
|Compiler warning (level 1) C4575|'`__vectorcall`' incompatible with the '`/clr`' option: converting to '`__stdcall`'|
|Compiler warning (level 1, Error) C4576|a parenthesized type followed by an initializer list is a non-standard explicit type conversion syntax|
|[Compiler warning (level 1, Off) C4577](compiler-warning-level-1-c4577.md)|'`noexcept`' used with no exception handling mode specified; termination on exception is not guaranteed. Specify `/EHsc`|
|Compiler warning (level 1, Error) C4578|'`abs`': conversion from '*type1*' to '*type2*', possible loss of data (Did you mean to call '*function*' or to `#include <cmath>`?)|
|[Compiler warning (level 3) C4580](compiler-warning-level-3-c4580.md)|`[attribute]` is deprecated; instead specify *namespace::*Attribute as a base class|
|[Compiler warning (level 1) C4581](compiler-warning-level-1-c4581.md)|deprecated behavior: '"*string*"' replaced with '*string*' to process attribute|
|Compiler warning (level 4, off) C4582|'*type*': constructor is not implicitly called|
|Compiler warning (level 4, off) C4583|'*type*': destructor is not implicitly called|
|[Compiler warning (level 1) C4584](compiler-warning-level-1-c4584.md)|'*class1*': base-class '*class2*' is already a base-class of '*class3*'|
|Compiler warning (level 1, Error) C4585|'*class*': A WinRT '`public ref class`' must either be sealed or derive from an existing unsealed class|
|Compiler warning (level 1, Error) C4586|'*type*': A public type cannot be declared in a top-level namespace called '`Windows`'|
|Compiler warning (level 1, off) C4587|'*anonymous_structure*': behavior change: constructor is no longer implicitly called|
|Compiler warning (level 1, off) C4588|'*anonymous_structure*': behavior change: destructor is no longer implicitly called|
|Compiler warning (level 4) C4589|Constructor of abstract class '*class1*' ignores initializer for virtual base class '*class2*'|
|Compiler warning (level 1, no longer emitted) C4591|'`constexpr`' call-depth limit of *number* exceeded (`/constexpr:depth<NUMBER>`)|
|Compiler warning (level 1, Error) C4592|'*name*': symbol will be dynamically initialized (implementation limitation)|
|Compiler warning (level 1, no longer emitted) C4593|'*function*': '`constexpr`' call evaluation step limit of '*limit*' exceeded; use `/constexpr:steps<NUMBER>` to increase the limit|
|Compiler warning (level 1) C4594|class '*name*' can never be instantiated - indirect virtual base class '*name*' is inaccessible|
|Compiler warning (level 3) C4595|'*name*': non-member operator new or delete functions may not be declared inline|
|[Compiler warning (level 4, Error, off) C4596](c4596.md)|'*identifier*': illegal qualified name in member declaration|
|[Compiler warning (error) C4597](c4597.md)|undefined behavior: *message*|
|Compiler warning (level 1 and level 3) C4598|'`#include "`*header*`"`': header number *number* in the precompiled header does not match current compilation at that position|
|Compiler warning (level 3, off) C4599|'*flag* *path*': command line argument number *number* does not match precompiled header|

## See also

[C/C++ Compiler and build tools errors and warnings](../compiler-errors-1/c-cpp-build-errors.md)\
[Compiler warnings C4000 - C5999](compiler-warnings-c4000-c5999.md)
