---
title: "ARM Exception Handling"
description: "Learn more about: ARM Exception Handling"
ms.date: 12/15/2021
ms.topic: concept-article
---
# ARM Exception Handling

Windows on ARM uses the same structured exception-handling mechanism for asynchronous hardware-generated exceptions and synchronous software-generated exceptions. Language-specific exception handlers are built on top of Windows structured exception handling by using language helper functions. This document describes exception handling in Windows on ARM, and the language helpers both the Microsoft ARM assembler and the MSVC compiler generate.

## ARM Exception Handling

Windows on ARM uses *unwind codes* to control stack unwinding during [structured exception handling](/windows/win32/debug/structured-exception-handling) (SEH). Unwind codes are a sequence of bytes stored in the `.xdata` section of the executable image. These codes describe the operation of function prologue and epilogue code in an abstract way. The handler uses them to undo the function prologue's effects when it unwinds to the caller's stack frame.

The ARM EABI (embedded application binary interface) specifies a model for exception unwinding that uses unwind codes. The model isn't sufficient for SEH unwinding in Windows. It must handle asynchronous cases where the processor is in the middle of the prologue or epilogue of a function. Windows also separates unwinding control into function-level unwinding and language-specific scope unwinding, which is unified in the ARM EABI. For these reasons, Windows on ARM specifies more details for the unwinding data and procedure.

### Assumptions

Executable images for Windows on ARM use the Portable Executable (PE) format. For more information, see [PE Format](/windows/win32/debug/pe-format). Exception handling information is stored in the `.pdata` and `.xdata` sections of the image.

The exception handling mechanism makes certain assumptions about code that follows the ABI for Windows on ARM:

- When an exception occurs within the body of a function, the handler could undo the prologue's operations, or do the epilogue's operations in a forward manner. Both should produce identical results.

- Prologues and epilogues tend to mirror each other. This feature can be used to reduce the size of the metadata needed to describe unwinding.

- Functions tend to be relatively small. Several optimizations rely on this observation for efficient packing of data.

- If a condition is placed on an epilogue, it applies equally to each instruction in the epilogue.

- If the prologue saves the stack pointer (SP) in another register, that register must remain unchanged throughout the function, so the original SP may be recovered at any time.

- Unless the SP is saved in another register, all manipulation of it must occur strictly within the prologue and epilogue.

- To unwind any stack frame, these operations are required:

  - Adjust r13 (SP) in 4-byte increments.

  - Pop one or more integer registers.

  - Pop one or more VFP (virtual floating-point) registers.

  - Copy an arbitrary register value to r13 (SP).

  - Load SP from the stack by using a small post-decrement operation.

  - Parse one of a few well-defined frame types.

### `.pdata` Records

The `.pdata` records in a PE-format image are an ordered array of fixed-length items that describe every stack-manipulating function. Leaf functions (functions that don't call other functions) don't require `.pdata` records when they don't manipulate the stack. (That is, they don't require any local storage and don't have to save or restore non-volatile registers.). Records for these functions can be omitted from the `.pdata` section to save space. An unwind operation from one of these functions can just copy the return address from the Link Register (LR) to the program counter (PC) to move up to the caller.

Every `.pdata` record for ARM is 8 bytes long. The general format of a record places the relative virtual address (RVA) of the function start in the first 32-bit word, followed by a second word that contains either a pointer to a variable-length `.xdata` block, or a packed word that describes a canonical function unwinding sequence, as shown in this table:

|Word Offset|Bits|Purpose|
|-----------------|----------|-------------|
|0|0-31|*`Function Start RVA`* is the 32-bit RVA of the start of the function. If the function contains thumb code, the low bit of this address must be set.|
|1|0-1|*`Flag`* is a 2-bit field that indicates how to interpret the remaining 30 bits of the second `.pdata` word. If *`Flag`* is 0, then the remaining bits form an *Exception Information RVA* (with the low two bits implicitly 0). If *`Flag`* is non-zero, then the remaining bits form a *Packed Unwind Data* structure.|
|1|2-31|*Exception Information RVA* or *Packed Unwind Data*.<br /><br /> *Exception Information RVA* is the address of the variable-length exception information structure, stored in the `.xdata` section. This data must be 4-byte aligned.<br /><br /> *Packed Unwind Data* is a compressed description of the operations required to unwind from a function, assuming a canonical form. In this case, no `.xdata` record is required.|

### Packed Unwind Data

For functions whose prologues and epilogues follow the canonical form described below, packed unwind data can be used. It eliminates the need for an `.xdata` record and significantly reduces the space required to provide the unwind data. The canonical prologues and epilogues are designed to meet the common requirements of a simple function that doesn't require an exception handler, and performs its setup and teardown operations in a standard order.

This table shows the format of a `.pdata` record that has packed unwind data:

| Word Offset | Bits | Purpose |
|--|--|--|
| 0 | 0-31 | *`Function Start RVA`* is the 32-bit RVA of the start of the function. If the function contains thumb code, the low bit of this address must be set. |
| 1 | 0-1 | *`Flag`* is a 2-bit field that has these meanings:<br /><br />- 00 = packed unwind data not used; remaining bits point to `.xdata` record.<br />- 01 = packed unwind data.<br />- 10 = packed unwind data where the function is assumed to have no prologue. This is useful for describing function fragments that are discontiguous with the start of the function.<br />- 11 = reserved. |
| 1 | 2-12 | *`Function Length`* is an 11-bit field that provides the length of the entire function in bytes divided by 2. If the function is larger than 4K bytes, a full `.xdata` record must be used instead. |
| 1 | 13-14 | *`Ret`* is a 2-bit field that indicates how the function returns:<br /><br />- 00 = return via pop {pc} (the *`L`* flag bit must be set to 1 in this case).<br />- 01 = return by using a 16-bit branch.<br />- 10 = return by using a 32-bit branch.<br />- 11 = no epilogue at all. This is useful for describing a discontiguous function fragment that may only contain a prologue, but whose epilogue is elsewhere. |
| 1 | 15 | *`H`* is a 1-bit flag that indicates whether the function "homes" the integer parameter registers (r0-r3) by pushing them at the start of the function, and deallocates the 16 bytes of stack before returning. (0 = doesn't home registers, 1 = homes registers.) |
| 1 | 16-18 | *`Reg`* is a 3-bit field that indicates the index of the last saved non-volatile register. If the *`R`* bit is 0, then only integer registers are being saved, and are assumed to be in the range of r4-rN, where N is equal to 4 + *`Reg`*. If the *`R`* bit is 1, then only floating-point registers are being saved, and are assumed to be in the range of d8-dN, where N is equal to 8 + *`Reg`*. The special combination of *`R`* = 1 and *`Reg`* = 7 indicates that no registers are saved. |
| 1 | 19 | *`R`* is a 1-bit flag that indicates whether the saved non-volatile registers are integer registers (0) or floating-point registers (1). If *`R`* is set to 1 and the *`Reg`* field is set to 7, no non-volatile registers were pushed. |
| 1 | 20 | *`L`* is a 1-bit flag that indicates whether the function saves/restores LR, along with other registers indicated by the *`Reg`* field. (0 = doesn't save/restore, 1 = does save/restore.) |
| 1 | 21 | *`C`* is a 1-bit flag that indicates whether the function includes extra instructions to set up a frame chain for fast stack walking (1) or not (0). If this bit is set, r11 is implicitly added to the list of integer non-volatile registers saved. (See restrictions below if the *`C`* flag is used.) |
| 1 | 22-31 | *`Stack Adjust`* is a 10-bit field that indicates the number of bytes of stack that are allocated for this function, divided by 4. However, only values between 0x000-0x3F3 can be directly encoded. Functions that allocate more than 4044 bytes of stack must use a full `.xdata` record. If the *`Stack Adjust`* field is 0x3F4 or larger, then the low 4 bits have special meaning:<br /><br />- Bits 0-1 indicate the number of words of stack adjustment (1-4) minus 1.<br />- Bit 2 is set to 1 if the prologue combined this adjustment into its push operation.<br />- Bit 3 is set to 1 if the epilogue combined this adjustment into its pop operation. |

Due to possible redundancies in the encodings above, these restrictions apply:

- If the *`C`* flag is set to 1:

  - The *`L`* flag must also be set to 1, because frame chaining requires both r11 and LR.

  - r11 must not be included in the set of registers described by *`Reg`*. That is, if r4-r11 are pushed, *`Reg`* should only describe r4-r10, because the *`C`* flag implies r11.

- If the *`Ret`* field is set to 0, the *`L`* flag must be set to 1.

Violating these restrictions causes an unsupported sequence.

For purposes of the discussion below, two pseudo-flags are derived from *`Stack Adjust`*:

- *`PF`* or "prologue folding" indicates that *`Stack Adjust`* is 0x3F4 or larger and bit 2 is set.

- *`EF`* or "epilogue folding" indicates that *`Stack Adjust`* is 0x3F4 or larger and bit 3 is set.

Prologues for canonical functions may have up to 5 instructions (notice that 3a and 3b are mutually exclusive):

| Instruction | Opcode is assumed present if: | Size | Opcode | Unwind Codes |
|--|--|--|--|--|
| 1 | *`H`*==1 | 16 | `push {r0-r3}` | 04 |
| 2 | *`C`*==1 or *`L`*==1 or *`R`*==0 or *`PF`*==1 | 16/32 | `push {registers}` | 80-BF/D0-DF/EC-ED |
| 3a | *`C`*==1 and (*`R`*==1 and *`PF`*==0) | 16 | `mov r11,sp` | FB |
| 3b | *`C`*==1 and (*`R`*==0 or *`PF`*==1) | 32 | `add r11,sp,#xx` | FC |
| 4 | *`R`*==1 and *`Reg`* != 7 | 32 | `vpush {d8-dE}` | E0-E7 |
| 5 | *`Stack Adjust`* != 0 and *`PF`*==0 | 16/32 | `sub sp,sp,#xx` | 00-7F/E8-EB |

Instruction 1 is always present if the *`H`* bit is set to 1.

To set up the frame chaining, either instruction 3a or 3b is present if the *`C`* bit is set. It is a 16-bit `mov` if no registers other than r11 and LR are pushed; otherwise, it is a 32-bit `add`.

If a non-folded adjustment is specified, instruction 5 is the explicit stack adjustment.

Instructions 2 and 4 are set based on whether a push is required. This table summarizes which registers are saved based on the *`C`*, *`L`*, *`R`*, and *`PF`* fields. In all cases, *`N`* is equal to *`Reg`* + 4, *`E`* is equal to *`Reg`* + 8, and *`S`* is equal to (~*`Stack Adjust`*) & 3.

| C | L | R | PF | Integer Registers Pushed | VFP Registers pushed |
|--|--|--|--|--|--|
| 0 | 0 | 0 | 0 | r4 - r*`N`* | none |
| 0 | 0 | 0 | 1 | r*`S`* - r*`N`* | none |
| 0 | 0 | 1 | 0 | none | d8 - d*`E`* |
| 0 | 0 | 1 | 1 | r*`S`* - r3 | d8 - d*`E`* |
| 0 | 1 | 0 | 0 | r4 - r*`N`*, LR | none |
| 0 | 1 | 0 | 1 | r*`S`* - r*`N`*, LR | none |
| 0 | 1 | 1 | 0 | LR | d8 - d*`E`* |
| 0 | 1 | 1 | 1 | r*`S`* - r3, LR | d8 - d*`E`* |
| 1 | 0 | 0 | 0 | (invalid encoding) | (invalid encoding) |
| 1 | 0 | 0 | 1 | (invalid encoding) | (invalid encoding) |
| 1 | 0 | 1 | 0 | (invalid encoding) | (invalid encoding) |
| 1 | 0 | 1 | 1 | (invalid encoding) | (invalid encoding) |
| 1 | 1 | 0 | 0 | r4 - r*`N`*, r11, LR | none |
| 1 | 1 | 0 | 1 | r*`S`* - r*`N`*, r11, LR | none |
| 1 | 1 | 1 | 0 | r11, LR | d8 - d*`E`* |
| 1 | 1 | 1 | 1 | r*`S`* - r3, r11, LR | d8 - d*`E`* |

The epilogues for canonical functions follow a similar form, but in reverse and with some additional options. The epilogue may be up to 5 instructions long, and its form is strictly dictated by the form of the prologue.

| Instruction | Opcode is assumed present if: | Size | Opcode |
|--|--|--|--|
| 6 | *`Stack Adjust`*!=0 and *`EF`*==0 | 16/32 | `add   sp,sp,#xx` |
| 7 | *`R`*==1 and *`Reg`*!=7 | 32 | `vpop  {d8-dE}` |
| 8 | *`C`*==1 or (*`L`*==1 and (*`H`*==0 or *`Ret`* !=0)) or *`R`*==0 or *`EF`*==1 | 16/32 | `pop   {registers}` |
| 9a | *`H`*==1 and (*`L`*==0 or *`Ret`*!=0) | 16 | `add   sp,sp,#0x10` |
| 9b | *`H`*==1 and *`L`*==1 and *`Ret`*==0 | 32 | `ldr   pc,[sp],#0x14` |
| 10a | *`Ret`*==1 | 16 | `bx    reg` |
| 10b | *`Ret`*==2 | 32 | `b     address` |

Instruction 6 is the explicit stack adjustment if a non-folded adjustment is specified. Because *`PF`* is independent of *`EF`*, it is possible to have instruction 5 present without instruction 6, or vice-versa.

Instructions 7 and 8 use the same logic as the prologue to determine which registers are restored from the stack, but with these three changes: first, *`EF`* is used in place of *`PF`*; second, if *`Ret`* = 0 and *`H`* = 0, then LR is replaced with PC in the register list and the epilogue ends immediately; third, if *`Ret`* = 0 and *`H`* = 1, then LR is omitted from the register list and popped by instruction 9b.

If *`H`* is set, then either instruction 9a or 9b is present. Instruction 9a is used when *`Ret`* is nonzero, which also implies the presence of either 10a or 10b. If L=1, then LR was popped as part of instruction 8. Instruction 9b is used when *`L`* is 1 and *`Ret`*  is zero, to indicate an early end to the epilogue, and to return and adjust the stack at the same time.

If the epilogue hasn't already ended, then either instruction 10a or 10b is present, to indicate a 16-bit or 32-bit branch, based on the value of *`Ret`*.

### `.xdata` Records

When the packed unwind format is insufficient to describe the unwinding of a function, a variable-length `.xdata` record must be created. The address of this record is stored in the second word of the `.pdata` record. The format of the `.xdata` is a packed variable-length set of words that has four sections:

1. A 1 or 2-word header that describes the overall size of the `.xdata` structure and provides key function data. The second word is only present if the *Epilogue Count* and *Code Words* fields are both set to 0. The fields are broken out in this table:

   |Word|Bits|Purpose|
   |----------|----------|-------------|
   |0|0-17|*`Function Length`* is an 18-bit field that indicates the total length of the function in bytes, divided by 2. If a function is larger than 512 KB, then multiple `.pdata` and `.xdata` records must be used to describe the function. For details, see the Large Functions section in this document.|
   |0|18-19|*Vers* is a 2-bit field that describes the version of the remaining`.xdata`. Only version 0 is currently defined; values of 1-3 are reserved.|
   |0|20|*X* is a 1-bit field that indicates the presence (1) or absence (0) of exception data.|
   |0|21|*`E`* is a 1-bit field that indicates that information that describes a single epilogue is packed into the header (1) rather than requiring additional scope words later (0).|
   |0|22|*F* is a 1-bit field that indicates that this record describes a function fragment (1) or a full function (0). A fragment implies that there's no prologue and that all prologue processing should be ignored.|
   |0|23-27|*Epilogue Count* is a 5-bit field that has two meanings, depending on the state of the *`E`* bit:<br /><br />- If *`E`* is 0, this field is a count of the total number of epilogue scopes described in section 2. If more than 31 scopes exist in the function, then this field and the *Code Words* field must both be set to 0 to indicate that an extension word is required.<br />- If *`E`* is 1, this field specifies the index of the first unwind code that describes the only epilogue.|
   |0|28-31|*Code Words* is a 4-bit field that specifies the number of 32-bit words required to contain all of the unwind codes in section 4. If more than 15 words are required for more than 63 unwind code bytes, this field and the *Epilogue Count* field must both be set to 0 to indicate that an extension word is required.|
   |1|0-15|*Extended Epilogue Count* is a 16-bit field that provides more space for encoding an unusually large number of epilogues. The extension word that contains this field is only present if the *Epilogue Count* and *Code Words* fields in the first header word are both set to 0.|
   |1|16-23|*Extended Code Words* is an 8-bit field that provides more space for encoding an unusually large number of unwind code words. The extension word that contains this field is only present if the *Epilogue Count* and *Code Words* fields in the first header word are both set to 0.|
   |1|24-31|Reserved|

1. After the exception data (if the *`E`* bit in the header was set to 0) is a list of information about epilogue scopes, which are packed one to a word and stored in order of increasing starting offset. Each scope contains these fields:

   |Bits|Purpose|
   |----------|-------------|
   |0-17|*Epilogue Start Offset* is an 18-bit field that describes the offset of the epilogue, in bytes divided by 2, relative to the start of the function.|
   |18-19|*Res* is a 2-bit field reserved for future expansion. Its value must be 0.|
   |20-23|*Condition* is a 4-bit field that gives the condition under which the epilogue is executed. For unconditional epilogues, it should be set to 0xE, which indicates "always". (An epilogue must be entirely conditional or entirely unconditional, and in Thumb-2 mode, the epilogue begins with the first instruction after the IT opcode.)|
   |24-31|*Epilogue Start Index* is an 8-bit field that indicates the byte index of the first unwind code that describes this epilogue.|

1. After the list of epilogue scopes comes an array of bytes that contain unwind codes, which are described in detail in the Unwind Codes section in this article. This array is padded at the end to the nearest full word boundary. The bytes are stored in little-endian order so that they can be directly fetched in little-endian mode.

1. If the *X* field in the header is 1, the unwind code bytes are followed by the exception handler information. This consists of one *Exception Handler RVA* that contains the address of the exception handler, followed immediately by the (variable-length) amount of data required by the exception handler.

The `.xdata` record is designed so that it is possible to fetch the first 8 bytes and compute the full size of the record, not including the length of the variable-sized exception data that follows. This code snippet computes the record size:

```cpp
ULONG ComputeXdataSize(PULONG Xdata)
{
    ULONG Size;
    ULONG EpilogueScopes;
    ULONG UnwindWords;

    if ((Xdata[0] >> 23) != 0) {
        Size = 4;
        EpilogueScopes = (Xdata[0] >> 23) & 0x1f;
        UnwindWords = (Xdata[0] >> 28) & 0x0f;
    } else {
        Size = 8;
        EpilogueScopes = Xdata[1] & 0xffff;
        UnwindWords = (Xdata[1] >> 16) & 0xff;
    }

    if (!(Xdata[0] & (1 << 21))) {
        Size += 4 * EpilogueScopes;
    }

    Size += 4 * UnwindWords;

    if (Xdata[0] & (1 << 20)) {
        Size += 4;  // Exception handler RVA
    }

    return Size;
}
```

Although the prologue and each epilogue has an index into the unwind codes, the table is shared between them. It's not uncommon that they can all share the same unwind codes. We recommend that compiler writers optimize for this case, because the largest index that can be specified is 255, and that limits the total number of unwind codes possible for a particular function.

### Unwind Codes

The array of unwind codes is a pool of instruction sequences that describe exactly how to undo the effects of the prologue, in the order in which the operations must be undone. The unwind codes are a mini instruction set, encoded as a string of bytes. When execution is complete, the return address to the calling function is in the LR register, and all non-volatile registers are restored to their values at the time the function was called.

If exceptions were guaranteed to only ever occur within a function body, and never within a prologue or epilogue, then only one unwind sequence would be necessary. However, the Windows unwinding model requires an ability to unwind from within a partially executed prologue or epilogue. To accommodate this requirement, the unwind codes have been carefully designed to have an unambiguous one-to-one mapping to each relevant opcode in the prologue and epilogue. This has several implications:

- It is possible to compute the length of the prologue and epilogue by counting the number of unwind codes. This is possible even with variable-length Thumb-2 instructions because there are distinct mappings for 16-bit and 32-bit opcodes.

- By counting the number of instructions past the start of an epilogue scope, it is possible to skip the equivalent number of unwind codes, and execute the rest of a sequence to complete the partially-executed unwind that the epilogue was performing.

- By counting the number of instructions before the end of the prologue, it is possible to skip the equivalent number of unwind codes, and execute the rest of the sequence to undo only those parts of the prologue that have completed execution.

The following table shows the mapping from unwind codes to opcodes. The most common codes are just one byte, while less common ones require two, three, or even four bytes. Each code is stored from most significant byte to least significant byte. The unwind code structure differs from the encoding described in the ARM EABI, because these unwind codes are designed to have a one-to-one mapping to the opcodes in the prologue and epilogue to allow for unwinding of partially executed prologues and epilogues.

| Byte 1 | Byte 2 | Byte 3 | Byte 4 | Opsize | Explanation |
|--|--|--|--|--|--|
| 00-7F |  |  |  | 16 | `add   sp,sp,#X`<br /><br /> where X is (Code & 0x7F) \* 4 |
| 80-BF | 00-FF |  |  | 32 | `pop   {r0-r12, lr}`<br /><br /> where LR is popped if Code & 0x2000 and r0-r12 are popped if the corresponding bit is set in Code & 0x1FFF |
| C0-CF |  |  |  | 16 | `mov   sp,rX`<br /><br /> where X is Code & 0x0F |
| D0-D7 |  |  |  | 16 | `pop   {r4-rX,lr}`<br /><br /> where X is (Code & 0x03) + 4 and LR is popped if Code & 0x04 |
| D8-DF |  |  |  | 32 | `pop   {r4-rX,lr}`<br /><br /> where X is (Code & 0x03) + 8 and LR is popped if Code & 0x04 |
| E0-E7 |  |  |  | 32 | `vpop  {d8-dX}`<br /><br /> where X is (Code & 0x07) + 8 |
| E8-EB | 00-FF |  |  | 32 | `addw  sp,sp,#X`<br /><br /> where X is (Code & 0x03FF) \* 4 |
| EC-ED | 00-FF |  |  | 16 | `pop   {r0-r7,lr}`<br /><br /> where LR is popped if Code & 0x0100 and r0-r7 are popped if the corresponding bit is set in Code & 0x00FF |
| EE | 00-0F |  |  | 16 | Microsoft-specific |
| EE | 10-FF |  |  | 16 | Available |
| EF | 00-0F |  |  | 32 | `ldr   lr,[sp],#X`<br /><br /> where X is (Code & 0x000F) \* 4 |
| EF | 10-FF |  |  | 32 | Available |
| F0-F4 |  |  |  | - | Available |
| F5 | 00-FF |  |  | 32 | `vpop  {dS-dE}`<br /><br /> where S is (Code & 0x00F0) >> 4 and E is Code & 0x000F |
| F6 | 00-FF |  |  | 32 | `vpop  {dS-dE}`<br /><br /> where S is ((Code & 0x00F0) >> 4) + 16 and E is (Code & 0x000F) + 16 |
| F7 | 00-FF | 00-FF |  | 16 | `add   sp,sp,#X`<br /><br /> where X is (Code & 0x00FFFF) \* 4 |
| F8 | 00-FF | 00-FF | 00-FF | 16 | `add   sp,sp,#X`<br /><br /> where X is (Code & 0x00FFFFFF) \* 4 |
| F9 | 00-FF | 00-FF |  | 32 | `add   sp,sp,#X`<br /><br /> where X is (Code & 0x00FFFF) \* 4 |
| FA | 00-FF | 00-FF | 00-FF | 32 | `add   sp,sp,#X`<br /><br /> where X is (Code & 0x00FFFFFF) \* 4 |
| FB |  |  |  | 16 | nop (16-bit) |
| FC |  |  |  | 32 | nop (32-bit) |
| FD |  |  |  | 16 | end + 16-bit nop in epilogue |
| FE |  |  |  | 32 | end + 32-bit nop in epilogue |
| FF |  |  |  | - | end |

This shows the range of hexadecimal values for each byte in an unwind code *Code*, along with the opcode size *Opsize* and the corresponding original instruction interpretation. Empty cells indicate shorter unwind codes. In instructions that have large values covering multiple bytes, the most significant bits are stored first. The *Opsize* field shows the implicit opcode size associated with each Thumb-2 operation. The apparent duplicate entries in the table with different encodings are used to distinguish between different opcode sizes.

The unwind codes are designed so that the first byte of the code tells both the total size in bytes of the code and the size of the corresponding opcode in the instruction stream. To compute the size of the prologue or epilogue, walk the unwind codes from the start of the sequence to the end, and use a lookup table or similar method to determine how long the corresponding opcode is.

Unwind codes 0xFD and 0xFE are equivalent to the regular end code 0xFF, but account for one extra nop opcode in the epilogue case, either 16-bit or 32-bit. For prologues, codes 0xFD, 0xFE and 0xFF are exactly equivalent. This accounts for the common epilogue endings `bx lr` or `b <tailcall-target>`, which don't have an equivalent prologue instruction. This increases the chance that unwind sequences can be shared between the prologue and the epilogues.

In many cases, it should be possible to use the same set of unwind codes for the prologue and all epilogues. However, to handle the unwinding of partially executed prologues and epilogues, you might have to have multiple unwind code sequences that vary in ordering or behavior. This is why each epilogue has its own index into the unwind array to show where to begin executing.

### Unwinding Partial Prologues and Epilogues

The most common unwinding case is when the exception occurs in the body of the function, away from the prologue and all epilogues. In this case, the unwinder executes the codes in the unwind array beginning at index 0 and continues until an end opcode is detected.

When an exception occurs while a prologue or epilogue is executing, the stack frame is only partially constructed, and the unwinder must determine exactly what has been done in order to correctly undo it.

For example, consider this prologue and epilogue sequence:

```asm
0000:   push  {r0-r3}         ; 0x04
0002:   push  {r4-r9, lr}     ; 0xdd
0006:   mov   r7, sp          ; 0xc7
...
0140:   mov   sp, r7          ; 0xc7
0142:   pop   {r4-r9, lr}     ; 0xdd
0146:   add   sp, sp, #16     ; 0x04
0148:   bx    lr
```

Next to each opcode is the appropriate unwind code to describe this operation. The sequence of unwind codes for the prologue is a mirror image of the unwind codes for the epilogue, not counting the final instruction. This case is common, and is the reason the unwind codes for the prologue are always assumed to be stored in reverse order from the prologue's execution order. This gives us a common set of unwind codes:

```asm
0xc7, 0xdd, 0x04, 0xfd
```

The 0xFD code is a special code for the end of the sequence that means that the epilogue is one 16-bit instruction longer than the prologue. This makes greater sharing of unwind codes possible.

In the example, if an exception occurs while the function body between the prologue and epilogue is executing, unwinding starts with the epilogue case, at offset 0 within the epilogue code. This corresponds to offset 0x140 in the example. The unwinder executes the full unwind sequence, because no cleanup has been done. If instead the exception occurs one instruction after the beginning of the epilogue code, the unwinder can successfully unwind by skipping the first unwind code. Given a one-to-one mapping between opcodes and unwind codes, if unwinding from instruction *n* in the epilogue, the unwinder should skip the first *n* unwind codes.

Similar logic works in reverse for the prologue. If unwinding from offset 0 in the prologue, nothing has to be executed. If unwinding from one instruction in, the unwind sequence should start one unwind code from the end because prologue unwind codes are stored in reverse order. In the general case, if unwinding from instruction *n* in the prologue, unwinding should start executing at *n* unwind codes from the end of the list of codes.

Prologue and epilogue unwind codes don't always match exactly. In that case, the unwind code array may have to contain several sequences of codes. To determine the offset to begin processing codes, use this logic:

1. If unwinding from within the body of the function, begin executing unwind codes at index 0 and continue until an end opcode is reached.

2. If unwinding from within an epilogue, use the epilogue-specific starting index provided by the epilogue scope. Calculate how many bytes the PC is from the start of the epilogue. Skip forward through the unwind codes until all of the already-executed instructions are accounted for. Execute the unwind sequence starting at that point.

3. If unwinding from within the prologue, start from index 0 in the unwind codes. Calculate the length of the prologue code from the sequence, and then calculate how many bytes the PC is from the end of the prologue. Skip forward through the unwind codes until all of the unexecuted instructions are accounted for. Execute the unwind sequence starting at that point.

The unwind codes for the prologue must always be the first in the array. they're also the codes used to unwind in the general case of unwinding from within the body. Any epilogue-specific code sequences should follow immediately after the prologue code sequence.

### Function Fragments

For code optimization, it may be useful to split a function into discontiguous parts. When this is done, each function fragment requires its own separate `.pdata`—and possibly `.xdata`—record.

Assuming that the function prologue is at the beginning of the function and can't be split, there are four function fragment cases:

- Prologue only; all epilogues in other fragments.

- Prologue and one or more epilogues; more epilogues in other fragments.

- No prologue or epilogues; prologue and one or more epilogues in other fragments.

- Epilogues only; prologue and possibly more epilogues in other fragments.

In the first case, only the prologue must be described. This can be done in compact `.pdata` form by describing the prologue normally and specifying a *`Ret`* value of 3 to indicate no epilogue. In the full `.xdata` form, this can be done by providing the prologue unwind codes at index 0 as usual, and specifying an epilogue count of 0.

The second case is just like a normal function. If there's only one epilogue in the fragment, and it is at the end of the fragment, then a compact `.pdata` record can be used. Otherwise, a full `.xdata` record must be used. Keep in mind that the offsets specified for the epilogue start are relative to the start of the fragment, not the original start of the function.

The third and fourth cases are variants of the first and second cases, respectively, except they don't contain a prologue. In these situations, it is assumed that there's code before the start of the epilogue and it is considered part of the body of the function, which would normally be unwound by undoing the effects of the prologue. These cases must therefore be encoded with a pseudo-prologue, which describes how to unwind from within the body, but which is treated as 0-length when determining whether to perform a partial unwind at the start of the fragment. Alternatively, this pseudo-prologue may be described by using the same unwind codes as the epilogue because they presumably perform equivalent operations.

In the third and fourth cases, the presence of a pseudo-prologue is specified either by setting the *`Flag`* field of the compact `.pdata` record to 2, or by setting the *F* flag in the `.xdata` header to 1. In either case, the check for a partial prologue unwind is ignored, and all non-epilogue unwinds are considered to be full.

#### Large Functions

Fragments can be used to describe functions larger than the 512 KB limit imposed by the bit fields in the `.xdata` header. To describe a larger function, just break it into fragments smaller than 512 KB. Each fragment should be adjusted so it doesn't split an epilogue into multiple pieces.

Only the first fragment of the function contains a prologue. All other fragments are marked as having no prologue. Depending on the number of epilogues, each fragment may contain zero or more epilogues. Keep in mind that each epilogue scope in a fragment specifies its starting offset relative to the start of the fragment, not the start of the function.

If a fragment has no prologue and no epilogue, it still requires its own `.pdata`—and possibly `.xdata`—record to describe how to unwind from within the body of the function.

#### Shrink-wrapping

A more complex special case of function fragments is called *shrink-wrapping*. It's a technique for deferring register saves from the start of the function to later in the function. It optimizes for simple cases that don't require register saving. This case has two parts: there's an outer region that allocates the stack space but saves a minimal set of registers, and an inner region that saves and restores other registers.

```asm
ShrinkWrappedFunction
    push   {r4, lr}          ; A: save minimal non-volatiles
    sub    sp, sp, #0x100    ; A: allocate all stack space up front
    ...                      ; A:
    add    r0, sp, #0xE4     ; A: prepare to do the inner save
    stm    r0, {r5-r11}      ; A: save remaining non-volatiles
    ...                      ; B:
    add    r0, sp, #0xE4     ; B: prepare to do the inner restore
    ldm    r0, {r5-r11}      ; B: restore remaining non-volatiles
    ...                      ; C:
    pop    {r4, pc}          ; C:
```

Shrink-wrapped functions are typically expected to pre-allocate the space for the extra register saves in the regular prologue, and then save the registers by using `str` or `stm` instead of `push`. This action keeps all stack-pointer manipulation in the function's original prologue.

The example shrink-wrapped function must be broken into three regions, which are marked as `A`, `B`, and `C` in the comments. The first `A` region covers the start of the function through the end of the additional non-volatile saves. A `.pdata` or `.xdata` record must be constructed to describe this fragment as having a prologue and no epilogues.

The middle `B` region gets its own `.pdata` or `.xdata` record that describes a fragment that has no prologue and no epilogue. However, the unwind codes for this region must still be present because it's considered a function body. The codes must describe a composite prologue that represents both the original registers saved in the region `A` prologue and the extra registers saved before entering region `B`, as if they were produced by one sequence of operations.

The register saves for region `B` can't be considered as an "inner prologue" because the composite prologue described for region `B` must describe both the region `A` prologue and the additional registers saved. If fragment `B` had a prologue, the unwind codes would also imply the size of that prologue, and there's no way to describe the composite prologue in a way that maps one-to-one with the opcodes that only save the additional registers.

The extra register saves must be considered part of region `A`, because until they're complete, the composite prologue doesn't accurately describe the state of the stack.

The last `C` region gets its own `.pdata` or `.xdata` record, describing a fragment that has no prologue but does have an epilogue.

An alternative approach can also work if the stack manipulation done before entering region `B` can be reduced to one instruction:

```asm
ShrinkWrappedFunction
    push   {r4, lr}          ; A: save minimal non-volatile registers
    sub    sp, sp, #0xE0     ; A: allocate minimal stack space up front
    ...                      ; A:
    push   {r4-r9}           ; A: save remaining non-volatiles
    ...                      ; B:
    pop    {r4-r9}           ; B: restore remaining non-volatiles
    ...                      ; C:
    pop    {r4, pc}          ; C: restore non-volatile registers
```

The key insight is that on each instruction boundary, the stack is fully consistent with the unwind codes for the region. If an unwind occurs before the inner push in this example, it's considered part of region `A`. Only the region `A` prologue is unwound. If the unwind occurs after the inner push, it's considered part of region `B`, which has no prologue. However, it has unwind codes that describe both the inner push and the original prologue from region `A`. Similar logic holds for the inner pop.

### Encoding Optimizations

The richness of the unwind codes, and the ability to make use of compact and expanded forms of data, provide many opportunities to optimize the encoding to further reduce space. With aggressive use of these techniques, the net overhead of describing functions and fragments by using unwind codes can be minimized.

The most important optimization idea: Don't confuse prologue and epilogue boundaries for unwinding purposes with logical prologue and epilogue boundaries from a compiler perspective. The unwinding boundaries can be shrunk and made tighter to improve efficiency. For example, a prologue may contain code after the stack setup to do verification checks. But once all the stack manipulation is complete, there's no need to encode further operations, and anything beyond that can be removed from the unwinding prologue.

This same rule applies to the function length. If there's data (such as a literal pool) that follows an epilogue in a function, it shouldn't be included as part of the function length. By shrinking the function to just the code that's part of the function, the chances are much greater that the epilogue is at the very end and a compact `.pdata` record can be used.

In a prologue, once the stack pointer is saved to another register, there's typically no need to record any further opcodes. To unwind the function, the first thing that's done is to recover SP from the saved register. Further operations don't have any effect on the unwind.

Single-instruction epilogues don't have to be encoded at all, either as scopes or as unwind codes. If an unwind takes place before that instruction is executed, then it's safe to assume it's from within the body of the function. Just executing the prologue unwind codes is sufficient. When the unwind takes place after the single instruction is executed, then by definition it takes place in another region.

Multi-instruction epilogues don't have to encode the first instruction of the epilogue, for the same reason as the previous point: if the unwind takes place before that instruction executes, a full prologue unwind is sufficient. If the unwind takes place after that instruction, then only the later operations have to be considered.

Unwind code reuse should be aggressive. The index each epilogue scope specifies points to an arbitrary starting point in the array of unwind codes. It doesn't have to point to the start of a previous sequence; it can point in the middle. The best approach is to generate the unwind code sequence. Then, scan for an exact byte match in the already-encoded pool of sequences. Use any perfect match as a starting point for reuse.

After single-instruction epilogues are ignored, if there are no remaining epilogues, consider using a compact `.pdata` form; it becomes much more likely in the absence of an epilogue.

## Examples

In these examples, the image base is at 0x00400000.

### Example 1: Leaf Function, No Locals

```asm
Prologue:
  004535F8: B430      push        {r4-r5}
Epilogue:
  00453656: BC30      pop         {r4-r5}
  00453658: 4770      bx          lr
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x000535F8 (= 0x004535F8-0x00400000)

- Word 1

  - *`Flag`* = 1, indicating canonical prologue and epilogue formats

  - *`Function Length`* = 0x31 (= 0x62/2)

  - *`Ret`* = 1, indicating a 16-bit branch return

  - *`H`* = 0, indicating the parameters weren't homed

  - *`R`* = 0 and *`Reg`* = 1, indicating push/pop of r4-r5

  - *`L`* = 0, indicating no LR save/restore

  - *`C`* = 0, indicating no frame chaining

  - *`Stack Adjust`* = 0, indicating no stack adjustment

### Example 2: Nested Function with Local Allocation

```asm
Prologue:
  004533AC: B5F0      push        {r4-r7, lr}
  004533AE: B083      sub         sp, sp, #0xC
Epilogue:
  00453412: B003      add         sp, sp, #0xC
  00453414: BDF0      pop         {r4-r7, pc}
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x000533AC (= 0x004533AC -0x00400000)

- Word 1

  - *`Flag`* = 1, indicating canonical prologue and epilogue formats

  - *`Function Length`* = 0x35 (= 0x6A/2)

  - *`Ret`* = 0, indicating a pop {pc} return

  - *`H`* = 0, indicating the parameters weren't homed

  - *`R`* = 0 and *`Reg`* = 3, indicating push/pop of r4-r7

  - *`L`* = 1, indicating LR was saved/restored

  - *`C`* = 0, indicating no frame chaining

  - *`Stack Adjust`* = 3 (= 0x0C/4)

### Example 3: Nested Variadic Function

```asm
Prologue:
  00453988: B40F      push        {r0-r3}
  0045398A: B570      push        {r4-r6, lr}
Epilogue:
  004539D4: E8BD 4070 pop         {r4-r6}
  004539D8: F85D FB14 ldr         pc, [sp], #0x14
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x00053988 (= 0x00453988-0x00400000)

- Word 1

  - *`Flag`* = 1, indicating canonical prologue and epilogue formats

  - *`Function Length`* = 0x2A (= 0x54/2)

  - *`Ret`* = 0, indicating a pop {pc}-style return (in this case an `ldr pc,[sp],#0x14` return)

  - *`H`* = 1, indicating the parameters were homed

  - *`R`* = 0 and *`Reg`* = 2, indicating push/pop of r4-r6

  - *`L`* = 1, indicating LR was saved/restored

  - *`C`* = 0, indicating no frame chaining

  - *`Stack Adjust`* = 0, indicating no stack adjustment

### Example 4: Function with Multiple Epilogues

```asm
Prologue:
  004592F4: E92D 47F0 stmdb       sp!, {r4-r10, lr}
  004592F8: B086      sub         sp, sp, #0x18
Epilogues:
  00459316: B006      add         sp, sp, #0x18
  00459318: E8BD 87F0 ldm         sp!, {r4-r10, pc}
  ...
  0045943E: B006      add         sp, sp, #0x18
  00459440: E8BD 87F0 ldm         sp!, {r4-r10, pc}
  ...
  004595D4: B006      add         sp, sp, #0x18
  004595D6: E8BD 87F0 ldm         sp!, {r4-r10, pc}
  ...
  00459606: B006      add         sp, sp, #0x18
  00459608: E8BD 87F0 ldm         sp!, {r4-r10, pc}
  ...
  00459636: F028 FF0F bl          KeBugCheckEx     ; end of function
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x000592F4 (= 0x004592F4-0x00400000)

- Word 1

  - *`Flag`* = 0, indicating `.xdata` record present (required for multiple epilogues)

  - *`.xdata` address* - 0x00400000

`.xdata` (variable, 6 words):

- Word 0

  - *`Function Length`* = 0x0001A3 (= 0x000346/2)

  - *`Vers`* = 0, indicating the first version of`.xdata`

  - *`X`* = 0, indicating no exception data

  - *`E`* = 0, indicating a list of epilogue scopes

  - *`F`* = 0, indicating a full function description, including prologue

  - *`Epilogue Count`* = 0x04, indicating the 4 total epilogue scopes

  - *`Code Words`* = 0x01, indicating one 32-bit word of unwind codes

- Words 1-4, describing 4 epilogue scopes at 4 locations. Each scope has a common set of unwind codes, shared with the prologue, at offset 0x00, and is unconditional, specifying condition 0x0E (always).

- Unwind codes, starting at Word 5: (shared between prologue/epilogue)

  - Unwind code 0 = 0x06: sp += (6 << 2)

  - Unwind code 1 = 0xDE: pop {r4-r10, lr}

  - Unwind code 2 = 0xFF: end

### Example 5: Function with Dynamic Stack and Inner Epilogue

```asm
Prologue:
  00485A20: B40F      push        {r0-r3}
  00485A22: E92D 41F0 stmdb       sp!, {r4-r8, lr}
  00485A26: 466E      mov         r6, sp
  00485A28: 0934      lsrs        r4, r6, #4
  00485A2A: 0124      lsls        r4, r4, #4
  00485A2C: 46A5      mov         sp, r4
  00485A2E: F2AD 2D90 subw        sp, sp, #0x290
Epilogue:
  00485BAC: 46B5      mov         sp, r6
  00485BAE: E8BD 41F0 ldm         sp!, {r4-r8, lr}
  00485BB2: B004      add         sp, sp, #0x10
  00485BB4: 4770      bx          lr
  ...
  00485E2A: F7FF BE7D b           #0x485B28    ; end of function
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x00085A20 (= 0x00485A20-0x00400000)

- Word 1

  - *`Flag`* = 0, indicating `.xdata` record present (needed for multiple epilogues)

  - *`.xdata` address* - 0x00400000

`.xdata` (variable, 3 words):

- Word 0

  - *`Function Length`* = 0x0001A3 (= 0x000346/2)

  - *`Vers`* = 0, indicating the first version of`.xdata`

  - *`X`* = 0, indicating no exception data

  - *`E`* = 0, indicating a list of epilogue scopes

  - *`F`* = 0, indicating a full function description, including prologue

  - *`Epilogue Count`* = 0x001, indicating the 1 total epilogue scope

  - *`Code Words`* = 0x01, indicating one 32-bit word of unwind codes

- Word 1: Epilogue scope at offset 0xC6 (= 0x18C/2), starting unwind code index at 0x00, and with a condition of 0x0E (always)

- Unwind codes, starting at Word 2: (shared between prologue/epilogue)

  - Unwind code 0 = 0xC6: sp = r6

  - Unwind code 1 = 0xDC: pop {r4-r8, lr}

  - Unwind code 2 = 0x04: sp += (4 << 2)

  - Unwind code 3 = 0xFD: end, counts as 16-bit instruction for epilogue

### Example 6: Function with Exception Handler

```asm
Prologue:
  00488C1C: 0059 A7ED dc.w  0x0059A7ED
  00488C20: 005A 8ED0 dc.w  0x005A8ED0
FunctionStart:
  00488C24: B590      push        {r4, r7, lr}
  00488C26: B085      sub         sp, sp, #0x14
  00488C28: 466F      mov         r7, sp
Epilogue:
  00488C6C: 46BD      mov         sp, r7
  00488C6E: B005      add         sp, sp, #0x14
  00488C70: BD90      pop         {r4, r7, pc}
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x00088C24 (= 0x00488C24-0x00400000)

- Word 1

  - *`Flag`* = 0, indicating `.xdata` record present (needed for multiple epilogues)

  - *`.xdata` address* - 0x00400000

`.xdata` (variable, 5 words):

- Word 0

  - *`Function Length`* =0x000027 (= 0x00004E/2)

  - *`Vers`* = 0, indicating the first version of`.xdata`

  - *`X`* = 1, indicating exception data present

  - *`E`* = 1, indicating a single epilogue

  - *`F`* = 0, indicating a full function description, including prologue

  - *`Epilogue Count`* = 0x00, indicating epilogue unwind codes start at offset 0x00

  - *`Code Words`* = 0x02, indicating two 32-bit words of unwind codes

- Unwind codes, starting at Word 1:

  - Unwind code 0 = 0xC7: sp = r7

  - Unwind code 1 = 0x05: sp += (5 << 2)

  - Unwind code 2 = 0xED/0x90: pop {r4, r7, lr}

  - Unwind code 4 = 0xFF: end

- Word 3 specifies an exception handler = 0x0019A7ED (= 0x0059A7ED - 0x00400000)

- Words 4 and beyond are inlined exception data

### Example 7: Funclet

```asm
Function:
  00488C72: B500      push        {lr}
  00488C74: B081      sub         sp, sp, #4
  00488C76: 3F20      subs        r7, #0x20
  00488C78: F117 0308 adds        r3, r7, #8
  00488C7C: 1D3A      adds        r2, r7, #4
  00488C7E: 1C39      adds        r1, r7, #0
  00488C80: F7FF FFAC bl          target
  00488C84: B001      add         sp, sp, #4
  00488C86: BD00      pop         {pc}
```

`.pdata` (fixed, 2 words):

- Word 0

  - *`Function Start RVA`* = 0x00088C72 (= 0x00488C72-0x00400000)

- Word 1

  - *`Flag`* = 1, indicating canonical prologue and epilogue formats

  - *`Function Length`* = 0x0B (= 0x16/2)

  - *`Ret`* = 0, indicating a pop {pc} return

  - *`H`* = 0, indicating the parameters weren't homed

  - *`R`* = 0 and *`Reg`* = 7, indicating no registers were saved/restored

  - *`L`* = 1, indicating LR was saved/restored

  - *`C`* = 0, indicating no frame chaining

  - *`Stack Adjust`* = 1, indicating a 1 × 4 byte stack adjustment

## See also

[Overview of ARM ABI Conventions](overview-of-arm-abi-conventions.md)<br/>
[Common Visual C++ ARM Migration Issues](common-visual-cpp-arm-migration-issues.md)
