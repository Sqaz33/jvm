# Run-Time Data Areas

The Java Virtual Machine defines various run-time data areas that are used during execution of a program. Some of these data areas are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. Other data areas are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits.

## Stack

Each Java Virtual Machine thread has a private *Java Virtual Machine stack*, created at the same time as the thread. A Java Virtual Machine stack stores frames A Java Virtual Machine stack is analogous to the stack of a conventional language such as C: it holds local variables and partial results, and plays a part in method invocation and return. Because the Java Virtual Machine stack is never manipulated directly except to push and pop frames, frames may be heap allocated. The memory for a Java Virtual Machine stack does not need to be contiguous.

## Heap

The Java Virtual Machine has a *heap* that is shared among all Java Virtual Machine threads. The heap is the run-time data area from which memory for all class instances and arrays is allocated.

## Method Area

The Java Virtual Machine has a *method area* that is shared among all Java Virtual Machine threads. The method area is analogous to the storage area for compiled code of a conventional language or analogous to the "text" segment in an operating system process. It stores per-class structures such as the run-time constant pool, field and method data, and the code for methods and constructors, including the special methods used in class and interface initialization and in instance initialization

## Constant Pool

A *run-time constant pool* is a per-class or per-interface run-time representation of the `constant_pool` table in a `class` file. It contains several kinds of constants, ranging from numeric literals known at compile-time to method and field references that must be resolved at run-time. The run-time constant pool serves a function similar to that of a symbol table for a conventional programming language, although it contains a wider range of data than a typical symbol table.

## Frames

A *frame* is used to store data and partial results, as well as to perform dynamic linking, return values for methods, and dispatch exceptions.

## Local Variables

Each frame contains an array of variables known as its *local variables*. The length of the local variable array of a frame is determined at compile-time and supplied in the binary representation of a class or interface along with the code for the method associated with the frame.

A single local variable can hold a value of type `boolean`, `byte`, `char`, `short`, `int`, `float`, `reference`, or `returnAddress`. A pair of local variables can hold a value of type `long` or `double`.

## Operands Stacks

Each frame contains a last-in-first-out (LIFO) stack known as its *operand stack*. The maximum depth of the operand stack of a frame is determined at compile-time and is supplied along with the code for the method associated with the frame.

Where it is clear by context, we will sometimes refer to the operand stack of the current frame as simply the operand stack.

The operand stack is empty when the frame that contains it is created. The Java Virtual Machine supplies instructions to load constants or values from local variables or fields onto the operand stack. Other Java Virtual Machine instructions take operands from the operand stack, operate on them, and push the result back onto the operand stack. The operand stack is also used to prepare parameters to be passed to methods and to receive method results.

For example, the *iadd* instruction adds two `int` values together. It requires that the `int` values to be added be the top two values of the operand stack, pushed there by previous instructions. Both of the `int` values are popped from the operand stack. They are added, and their sum is pushed back onto the operand stack. Subcomputations may be nested on the operand stack, resulting in values that can be used by the encompassing computation.

## Types and the Java Virtual Machine

Most of the instructions in the Java Virtual Machine instruction set encode type information about the operations they perform. For instance, the *iload* instruction loads the contents of a local variable, which must be an `int`, onto the operand stack. The *fload* instruction does the same with a `float` value. The two instructions may have identical implementations, but have distinct opcodes.

## Dynamic Linking

Each frame ([§2.6](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.6 "2.6. Frames")) contains a reference to the run-time constant pool ([§2.5.5](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.5.5 "2.5.5. Run-Time Constant Pool")) for the type of the current method to support *dynamic linking* of the method code. The `class` file code for a method refers to methods to be invoked and variables to be accessed via symbolic references. Dynamic linking translates these symbolic method references into concrete method references, loading classes as necessary to resolve as-yet-undefined symbols, and translates variable accesses into appropriate offsets in storage structures associated with the run-time location of these variables.

## Normal Method Invocation Completion

A method invocation *completes normally* if that invocation does not cause an exception ([§2.10](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.10 "2.10. Exceptions")) to be thrown, either directly from the Java Virtual Machine or as a result of executing an explicit `throw` statement. If the invocation of the current method completes normally, then a value may be returned to the invoking method. This occurs when the invoked method executes one of the return instructions ([§2.11.8](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.11.8 "2.11.8. Method Invocation and Return Instructions")), the choice of which must be appropriate for the type of the value being returned (if any).

The current frame ([§2.6](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.6 "2.6. Frames")) is used in this case to restore the state of the invoker, including its local variables and operand stack, with the program counter of the invoker appropriately incremented to skip past the method invocation instruction. Execution then continues normally in the invoking method's frame with the returned value (if any) pushed onto the operand stack of that frame.

## Abrupt Method Invocation Completion

A method invocation *completes abruptly* if execution of a Java Virtual Machine instruction within the method causes the Java Virtual Machine to throw an exception ([§2.10](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.10 "2.10. Exceptions")), and that exception is not handled within the method. Execution of an *athrow* instruction ([§*athrow*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.athrow "athrow")) also causes an exception to be explicitly thrown and, if the exception is not caught by the current method, results in abrupt method invocation completion. A method invocation that completes abruptly never returns a value to its invoker.

# Floating-Point Arithmetic

The Java Virtual Machine incorporates a subset of the floating-point arithmetic specified in *IEEE Standard for Binary Floating-Point Arithmetic* (ANSI/IEEE Std. 754-1985, New York).

# Special Methods

## Instance \<init\>

A class has zero or more *instance initialization methods*, each typically corresponding to a constructor written in the Java programming language.

A method is an instance initialization method if all of the following are true:

* It is defined in a class (not an interface).
* It has the special name `<init>`.
* It is `void` ([§4.3.3](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.3.3 "4.3.3. Method Descriptors")).

The declaration and use of an instance initialization method is constrained by the Java Virtual Machine. For the declaration, the method's `access_flags` item and `code` array are constrained ([§4.6](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.6 "4.6. Methods"), [§4.9.2](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.9.2 "4.9.2. Structural Constraints")). For a use, an instance initialization method may be invoked only by the *invokespecial* instruction on an uninitialized class instance.

## class \<clinit\>

A class or interface has at most one *class or interface initialization method* and is initialized by the Java Virtual Machine invoking that method ([§5.5](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-5.html#jvms-5.5 "5.5. Initialization")).

A method is a *class or interface initialization method* if all of the following are true:

* It has the special name `<clinit>`.
* It is `void` ([§4.3.3](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.3.3 "4.3.3. Method Descriptors")).
* In a `class` file whose version number is 51.0 or above, the method has its `ACC_STATIC` flag set and takes no arguments ([§4.6](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.6 "4.6. Methods")).

(Для static поле и блоков)

# Exceptions

An exception in the Java Virtual Machine is represented by an instance of the class `Throwable` or one of its subclasses. Throwing an exception results in an immediate nonlocal transfer of control from the point where the exception was thrown. ... An *athrow* instruction ([§*athrow*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.athrow "athrow")) was executed.

Each method in the Java Virtual Machine may be associated with zero or more *exception handlers*. An exception handler specifies the range of offsets into the Java Virtual Machine code implementing the method for which the exception handler is active, describes the type of exception that the exception handler is able to handle, and specifies the location of the code that is to handle that exception. An exception matches an exception handler if the offset of the instruction that caused the exception is in the range of offsets of the exception handler and the exception type is the same class as or a subclass of the class of exception that the exception handler handles. When an exception is thrown, the Java Virtual Machine searches for a matching exception handler in the current method. If a matching exception handler is found, the system branches to the exception handling code specified by the matched handler.

If no such exception handler is found in the current method, the current method invocation completes abruptly ([§2.6.5](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.6.5 "2.6.5. Abrupt Method Invocation Completion")). On abrupt completion, the operand stack and local variables of the current method invocation are discarded, and its frame is popped, reinstating the frame of the invoking method. The exception is then rethrown in the context of the invoker's frame and so on, continuing up the method invocation chain. If no suitable exception handler is found before the top of the method invocation chain is reached, the execution of the thread in which the exception was thrown is terminated.

# Instruction Set Summary

```java
do {
    atomically calculate pc and fetch opcode at pc;
    if (operands) fetch operands;
    execute the action for the opcode;
} while (there is more to do);
```
