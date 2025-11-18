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

Each entry on the operand stack can hold a value of any Java Virtual Machine type, including a value of type `long` or type `double`.

Values from the operand stack must be operated upon in ways appropriate to their types. It is not possible, for example, to push two `int` values and subsequently treat them as a `long` or to push two `float` values and subsequently add them with an *iadd* instruction. A small number of Java Virtual Machine instructions (the *dup* instructions ([§*dup*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.dup "dup")) and *swap* ([§*swap*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.swap "swap"))) operate on run-time data areas as raw values without regard to their specific types; these instructions are defined in such a way that they cannot be used to modify or break up individual values. These restrictions on operand stack manipulation are enforced through `class` file verification ([§4.10](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html#jvms-4.10 "4.10. Verification of class Files")).

At any point in time, an operand stack has an associated depth, where a value of type `long` or `double` contributes two units to the depth and a value of any other type contributes one unit.

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

# Types and The JVM

Most of the instructions in the Java Virtual Machine instruction set encode type information about the operations they perform. For instance, the *iload* instruction ([§*iload*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.iload "iload")) loads the contents of a local variable, which must be an `int`, onto the operand stack. The *fload* instruction ([§*fload*](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html#jvms-6.5.fload "fload")) does the same with a `float` value. The two instructions may have identical implementations, but have distinct opcodes.

# Instructions

## Store And Load

* Load a local variable onto the operand stack: *iload*, *iload\_<n>*, *lload*, *lload\_<n>*, *fload*, *fload\_<n>*, *dload*, *dload\_<n>*, *aload*, *aload\_<n>*.
* Store a value from the operand stack into a local variable: *istore*, *istore\_<n>*, *lstore*, *lstore\_<n>*, *fstore*, *fstore\_<n>*, *dstore*, *dstore\_<n>*, *astore*, *astore\_<n>*.
* Load a constant on to the operand stack: *bipush*, *sipush*, *ldc*, *ldc\_w*, *ldc2\_w*, *aconst\_null*, *iconst\_m1*, *iconst\_<i>*, *lconst\_<l>*, *fconst\_<f>*, *dconst\_<d>*.

Instructions that access fields of objects and elements of arrays ([§2.11.5](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.11.5 "2.11.5. Object Creation and Manipulation")) also transfer data to and from the operand stack.

## Arithmetic instr

The arithmetic instructions compute a result that is typically a function of two values on the operand stack, pushing the result back on the operand stack. There are two main kinds of arithmetic instructions: those operating on integer values and those operating on floating-point values. Within each of these kinds, the arithmetic instructions are specialized to Java Virtual Machine numeric types.

## Object Creation And Manipulation

Although both class instances and arrays are objects, the Java Virtual Machine creates and manipulates class instances and arrays using distinct sets of instructions:

* Create a new class instance: *new*.
* Create a new array: *newarray*, *anewarray*, *multianewarray*.
* Access fields of classes (`static` fields, known as class variables) and fields of class instances (non-`static` fields, known as instance variables): *getstatic*, *putstatic*, *getfield*, *putfield*.
* Load an array component onto the operand stack: *baload*, *caload*, *saload*, *iaload*, *laload*, *faload*, *daload*, *aaload*.
* Store a value from the operand stack as an array component: *bastore*, *castore*, *sastore*, *iastore*, *lastore*, *fastore*, *dastore*, *aastore*.
* Get the length of array: *arraylength*.
* Check properties of class instances or arrays: *instanceof*, *checkcast*.

## Stack ops

A number of instructions are provided for the direct manipulation of the operand stack: *pop*, *pop2*, *dup*, *dup2*, *dup\_x1*, *dup2\_x1*, *dup\_x2*, *dup2\_x2*, *swap*.

## Branches

* Conditional branch: *ifeq*, *ifne*, *iflt*, *ifle*, *ifgt*, *ifge*, *ifnull*, *ifnonnull*, *if\_icmpeq*, *if\_icmpne*, *if\_icmplt*, *if\_icmple*, *if\_icmpgt* *if\_icmpge*, *if\_acmpeq*, *if\_acmpne*.
* Compound conditional branch: *tableswitch*, *lookupswitch*.
* Unconditional branch: *goto*, *goto\_w*, *jsr*, *jsr\_w*, *ret*.

## Invocation and Return Instruction

The following five instructions invoke methods:

* *invokevirtual* invokes an instance method of an object, dispatching on the (virtual) type of the object. This is the normal method dispatch in the Java programming language.
* *invokeinterface* invokes an interface method, searching the methods implemented by the particular run-time object to find the appropriate method.
* *invokespecial* invokes an instance method requiring special handling, either an instance initialization method ([§2.9.1](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.9.1 "2.9.1. Instance Initialization Methods")) or a method of the current class or its supertypes.
* *invokestatic* invokes a class (`static`) method in a named class.

## Throw Exception

An exception is thrown programmatically using the *athrow* instruction. Exceptions can also be thrown by various Java Virtual Machine instructions if they detect an abnormal condition.
