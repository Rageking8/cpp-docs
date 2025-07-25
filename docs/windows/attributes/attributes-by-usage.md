---
description: "Learn more about: Attributes by Usage"
title: "Attributes by Usage"
ms.custom: "index-page"
ms.date: "10/02/2018"
ms.topic: "concept-article"
helpviewer_keywords: ["attributes [C++/CLI]"]
ms.assetid: 8be2de10-b1ff-4ca4-a114-75318408593c
---
# Attributes by Usage

This topic lists attributes according to the C++ language elements to which they apply.

If an attribute precedes an element that is not in the attribute's scope, the attribute block is treated as a comment.

|Attribute|Description|
|---------------|-----------------|
|[Module Attributes](module-attributes.md)|Applies to the [module](module-cpp.md) attribute.|
|[Interface Attributes](interface-attributes.md)|Applies to the [__interface](../../cpp/interface.md) C++ keyword.|
|[Class Attributes](class-attributes.md)|Applies to the C++ keyword.|
|[Method Attributes](method-attributes.md)|Applies to the methods in a class, coclass, or interface.|
|[Parameter Attributes](parameter-attributes.md)|Applies to parameters of a method in a class or interface.|
|[Data Member Attributes](data-member-attributes.md)|Applies to the data members in a class, coclass, or interface.|
|[Typedef, Enum, Union, and Struct Attributes](typedef-enum-union-and-struct-attributes.md)|Applies to the C++ keywords.|
|[Array Attributes](array-attributes.md)|Applies to arrays or `SAFEARRAY`s.|
|[Stand-Alone Attributes](stand-alone-attributes.md)|Operates more like a line of code but does not operate on a C++ keyword. Stand-alone attribute statements require a semicolon at the end of the line.|
|[Custom Attributes](custom-attributes-cpp.md)|Allows the user to extend metadata.|

## Module Attributes

The following attribute can only be applied to the [module](module-cpp.md) attribute.

|Attribute|Description|
|---------------|-----------------|
|[helpstringdll](helpstringdll.md)|Specifies the name of the DLL to use to perform document string lookup (localization).|

## Interface Attributes

The following attributes apply to the [interface (or __interface)](../../cpp/interface.md) C++ keyword.

|Attribute|Description|
|---------------|-----------------|
|[async_uuid](async-uuid.md)|Specifies the UUID that directs the MIDL compiler to define both synchronous and asynchronous versions of a COM interface.|
|[custom](custom-cpp.md)|Lets you define your own attributes.|
|[dispinterface](dispinterface.md)|Places an interface in the .idl file as a dispatch interface.|
|[dual](dual.md)|Places an interface in the .idl file as a dual interface.|
|[export](export.md)|Causes a data structure to be placed in the .idl file.|
|[helpcontext](helpcontext.md)|Specifies a context ID that lets the user view information about this element in the Help file.|
|[helpfile](helpfile.md)|Sets the name of the Help file for a type library.|
|[helpstring](helpstring.md)|Specifies a character string that is used to describe the element to which it applies.|
|[helpstringcontext](helpstringcontext.md)|Specifies the ID of a help topic in an .hlp or .chm file.|
|[helpstringdll](helpstringdll.md)|Specifies the name of the DLL to use to perform document string lookup (localization).|
|[hidden](hidden.md)|Indicates that the item exists but should not be displayed in a user-oriented browser.|
|[library_block](library-block.md)|Places a construct inside the .idl file's library block.|
|[local](local-cpp.md)|Allows you to use the MIDL compiler as a header generator when used in the interface header. When used in an individual function, designates a local procedure for which no stubs are generated.|
|[nonextensible](nonextensible.md)|Specifies that the `IDispatch` implementation includes only the properties and methods listed in the interface description and cannot be extended with additional members at run time. This attribute is only valid on a [dual](dual.md) interface.|
|[odl](odl.md)|Identifies an interface as an Object Description Language (ODL) interface.|
|[object](object-cpp.md)|Identifies a custom interface.|
|[oleautomation](oleautomation.md)|Indicates that an interface is compatible with Automation.|
|[pointer_default](pointer-default.md)|Specifies the default pointer attribute for all pointers except top-level pointers that appear in parameter lists.|
|[ptr](ptr.md)|Designates a pointer as a full pointer.|
|[restricted](restricted.md)|Designates which members of the library cannot be called arbitrarily.|
|[uuid](uuid-cpp-attributes.md)|Provides the unique ID for the library|

You must observe these rules for defining an interface:

- Default calling convention is [__stdcall](../../cpp/stdcall.md).

- A GUID is supplied for you if you do not supply one.

- No overloaded methods are allowed.

When not specifying the [uuid](uuid-cpp-attributes.md) attribute and using the same interface name in different attribute projects, the same GUID is generated.

## See also

[C++ Attributes for COM and .NET](cpp-attributes-com-net.md)<br/>
[Attributes by Group](attributes-by-group.md)<br/>
[Attributes Alphabetical Reference](attributes-alphabetical-reference.md)
