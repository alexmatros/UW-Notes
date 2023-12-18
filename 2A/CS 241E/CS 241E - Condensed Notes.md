# Chapter 1 - Introduction
## About the Course
- Goal: build abstractions to represent higher-level concepts
## Interpreting Bits
- Bits on their own have no inherent meaning, just ones and zeros
- Can define conventions for interpreting bits by mapping bit sequences to concepts
	- Allow us to define binary numbers, ascii chars, etc.
## Binary Numbers
- A common mapping from bit sequences is the binary encoding of numbers: each bit is a coefficient of a power of 2
- BITS -> NUM
	- Sum up the powers of two that correspond to the 1-bits in the sequence
- NUM -> BITS
	- Repeatedly divide the desired number by 2, record the remainders, and read them in reverse
- Binary number convention maps sequences of $n$ bits in the range: \[0, $2^n-1$\]
## Negative Numbers - Two's Complement
- A common convention for representing signed integers is two's complement
- In this convention, bits are also coefficients of powers of two, with the first power of two negated
- Two's complement convention maps sequences of $n$ bits in the range: \[$-2^{n-1}, 2^{n-1}-1$\]
- Convenient property of two's complement: the corresponding coefficients in the binary and two's complement interpretations are all congruent to each other modulo $2^n$, so sums are congruent to each other
	- Benefit is if computer hardware implements arithmetic modulo $2^n$, same hardware can be used for add/sub/mult on both unsigned binary numbers and signed two's complement numbers
- Find the two's complement representation of any negative number, $-x$:
	1. Write the unsigned binary encoding of the positive num $x$
	2. Flip all its bits
	3. Add 1 to result
# Chapter 2 - The MIPS Computer
- Simple computer model:
![[Pasted image 20231216153200.png]]
- Memory
	- An array of $2^{27}$ bits
	- Bits are grouped into $2^{22}$ **words** of 32 bits each
	- Each word in memory is labelled with an **address**, or sequence of 32 bits
	- Each address, interpreted as a binary number, is a multiple of 4 in the range \[$0$, $2^{24}-4$\]
- Central Processing Unit (CPU)
	- Contains smaller amount of memory called registers, in our case 34 registers of 32 bits each
	- It also contains a control unit and an arithmetic logic unit (ALU)
- The state of a the computer at a given time is the values of all the bits in the registers and the memory
- The control unit and ALU implement a function that maps each possible state to some new state, a **step**
## Stored Program Architecture
- The step function is an encoding of the **program** we want the computer to run
- A **stored program computer** is one that implements a general step function that is able to implement arbitrary programs.
- The convention used to encode instructions of a program into a sequence of bits stored in memory to be executed by a CPU is **machine language**
- **Code is data!** - enables code to be manipulated as data
## A General-Purpose Step Function
- The **fetch-increment-execute cycle**
	1. The word (instruction) at the address stored in PC register is **fetched** (read)
	2. PC **incremented** by 4
	3. The fetched instruction is **executed**
## MIPS Machine Language Instructions
- ADD/SUB
	- Add/subtract numbers in registers $s$, $t$ and place the result in register $d$
	- Arithmetic is preformed modulo $2^{32}$, so same instr can be used regardless of signed vs. unsigned
- MULT/MULTU
	- Performs multiplication modulo $2^{64}$ thus is necessary to have one for signed and unsigned
	- Stores the first (most-significant) 32 bits in the HI register, and the remaining (least-significant) 32 bits in the LO register
- MVFH/MVFL
	- Copies the values in HI/LO registers into one of the numbered registers
- DIV/DIVU
	- Perform integer division, encoding integers in signed two's complement or unsigned binary respectively
	- The integer part of the quotient is stored in the LO register, the remainder is stored in the HI register
- LIS
	- Reads the value at the memory address given by PC into register $d$
	- Immediately increments the PC by 4
- LW/SW
	- LW copies a word from an address in memory into a register
	- SW stores a word from a register into an address in memory
- SLT/SLTU
	- Compares the values in registers, sets the result register $d$ to 1 if the value if $s$ is less than the value of $t$; 0 otherwise
	- Compares two's complement and unsigned binary respectively
- BEQ/BNE
	- Compares the values of registers, if they are equal (or not equal), adds the constant $i*4$ to the PC, so the execution of the program proceeds $i$ words after the next instruction
- JR
	- Copied the value of register $s$ into the PC
	- The next instruction that will execute will be the instruction whose address was in register $s$
- JALR
	- Does the same as JR, but saves the previous value of PC into register 31
## Assembly Language
- Simple but practical abstraction is **opcodes**: symbolic names for machine language instructions
- A language that uses opcodes to designate the bit sequences of machine language instructions is called **assembly language**
- A program that translates from assembly languages to machine language is called an **assembler**
- Each instruction in an assembly language program corresponds directly to an instruction in the resulting machine language program
## Labels
- A **label** is a name that represents a memory address
- Labels are associated with two operations:
	- **Defining** a label in an assembly language program associates the name with the memory address at which the assembly language instruction after the label will be placed
	- **Using** a label inserts the memory address corresponding to the label in the machine language code
## Implementing Labels
- Two passes over the code are necessary to eliminate labels from an assembly language program and translate them to memory addresses and offsets
	1. The assembler determines the memory address of each instruction in the program, determines the memory address of each label definition; then it builds a **symbol table** mapping the names of the labels to their memory addresses
	2. Assembler translates each assembly language instruction into the corresponding machine language word and translates each use of a label into the corresponding memory address or offset; uses the symbol table to look up the address denoted by each label
# Chapter 3 - Variables
## Variable Extents
- The **extent** of a variable instance is the time interval during program execution when the variable can be accessed
- Various extents:
	- **Local variables** have an extent from when the procedure is called to when it returns; procedure calls follow last-in-first-out order, so it is natural to use a *stack* data structure to implement local vars
	- **Global variables** have only one instance and its extent is the duration of the execution of the program; natural to implement with a *fixed memory address* or *register*
	- **Fields of objects/records** have an extent from when the object is created and ends when it is freed, either explicitly by the program or automatically by garbage collector; extent is arbitrary so implement with a *heap* data structure
## Implementing a Stack
- Stack is implemented at the end (most significant) of memory locations
	- Grows towards smaller memory directions, shrinks to larger addr 
- Keep a **stack pointer**, or address to the top of the stack
	- To *push* a word $w$ onto the stack, sub 4 from the SP so it contains the address of an unused word, and store $w$ at this new memory address in SP
	- To *pop* a word from the stack, load the word from the memory address given by SP and add 4 to SP so it contains the address of the next word on the stack
- MIPS computer initialized register 30 to be the size of memory, one word past the address of the last word in memory, when it begins executing a program
## Stack Frames and the Frame Pointer
- The area of the stack reserved for the variables instances of an invocation of a procedure is called a **stack frame** or **activation record**
- When a stack frame is allocated, a copy of the new SP is placed into the **frame pointer** (FP) register; this holds the address of the stack frame throughout the execution of the procedure, while the SP is free to be used/grow as needed to store temps, etc.
- Compiler keeps track of all local vars in a proc and assigns them a unique offset from the FP
	- Maintains a symbol table that maps each variable to its offset
	- Memory address of each var is calculated by adding their offset to the address in FP
- Summary:
	1. The *stack pointer* is a register that, at *run time*, holds the address of the top word on the stack.
	2. The *frame pointer* is a register that, at *run time*, indicates the location in memory of the frame of the currently executing procedure. Variables are accessed at constant offsets from the address in the frame pointer. 
	3. The *symbol table* of each procedure is a data structure that exists at *compile time*. It maps each local variable in the procedure to a constant offset from the frame pointer.
## Chunks 
- **Chunks** are a collection of some known number of words stored at consecutive memory locations
- For now, a chunk stores just a frame (the local variables of a proc) - will explore chunks storing other data later
- The first word of the chunk stores the total size of the chunk in bytes
- The second word is reserved for A11
- The remaining words contain arbitrary data to be stored in the chunk, such as values of local variables when the chunk implements the frame of a procedure
# Chapter 4 - Expressions
## Technique 1: Using a Stack
- Simply push arbitrarily many values onto the stack, allowing us to evaluate expressions of arbitrary depth
- In general, code to evaluate $e_{1}\odot e_2$:
	1. Evaluate $e_1$, save its value on the stack
	2. Evaluate $e_2$, pop the value of $e_1$ off the stack
	3. Apply the arithmetic operator to the two values
- The stack is necessary so that the evaluation of $e_2$ does not overwrite the result obtained from evaluating $e_1$
- PRO: technique is *general* enough for expressions of arbitrary depth
- CON: lots of pushes/pops on the stack, thus not very *efficient*
- CON: the use of the stack makes the generated code *complicated*
## Technique 2: Using Variables
- Use variables to create arbitrarily many fresh variables whenever convenient to evalutate an expression
- PRO: technique is *simple* and flexible, easy for compiler to improve and further optimize
- CON: machine language arithmetic instructions *operate on registers, not directly on variables*; cannot directly express expressions as a single machine language instruction
- CON: to further translate this code into efficient assembly lang, need a way to map vars to registers, rather than to stack locations, and *map a large number of vars to limited registers*
- Technique that alleviates the last two disadvantages is called *register allocation*
## Technique 3: Vars for Temp Values, Arithmetic on Regs
- Return to the convention that the code generated to evaluate an expression leaves the resulting value in register 3, instead of a variable
	- This is bc machine lang arithmetic instructions require values in register, not variables
- Instead of using the stack to store temp values, will create fresh variables to store the value of each subexpression
- In general, code to evaluate $e_{1}\odot e_2$:
	1. Evaluate $e_1$, result in Reg.result
	2. Write Reg.result into variable $t_1$
	3. Evaluate $e_2$, result in Reg.result
	4. Read variable $t_1$ into Reg.scratch (now Reg.scratch has value of $e_1$, Reg.result value of $e_2$)
	5. Apply operator to Reg.scratch and Reg.result, store result in Reg.result
- It is equally as inefficient as technique 1, but less complicated than technique 2 so will use this
- PRO: code generated is easier to understand than technique 2, and easier to optimize further bc the value of each subexpression is in its own variable rather than different stack locations
- PRO: code generated can be translated directly to assembly language without requiring a register allocator, unlike technique 2

- Real-world compiler would implement technique 2 for its efficiency, but we will use technique 3 to avoid complication of register allocation
## Keeping Track of Temp Vars
- Use scopes to keep track of temporary variables
- A **scope** defines one or more new variables and groups them together with a fragment of code in which those variables can be accessed
- By creating a scope for every temporary variable we create, we can easily make a list of all the temporary variables by finding all of the scopes in the generated code and reading the variable lists from them
## Control Structures
- If statement:
```scala
/* Generate assembly language code to implement the if statement 156 * if( e1 op e2 ) T else E. */ 
def generateCode(e1, operator, e2, T, E): Code = { 
	t1 = new Variable 
	elseLabel = new Label 
	endLabel = new Label 
	Scope (t1, 
		block(
			// code to evaluate subexpression e1 
			// code to write Reg.result into variable t1 
			// code to evaluate other subexpression e2 
			// code to read variable t1 into Reg.scratch
			// now Reg.scratch has value of e1; Reg.result has value of e2
			// code to apply operator to Reg.scratch and Reg.result and
			//   branch to elseLabel if the result of the comparison is false 
			//   (using beq or bne) 
			T 
			beq(Reg.zero, Reg.zero, endLabel)
			Define(elseLabel)
			E
			Define(endLabel)
		)
	)
}
```
- Code for while loops is similar
## Arrays
- An **array** is a collection of elements indexed by consecutive natural numbers
- Value stored in each element can be read/written efficiently using its index
- Can be implemented by using a contiguous range of memory addresses to store the values of the array elements; each element is assigned the same amount of memory
	- To calculate the memory address of any element of the array given its index $i$ is: multiply $i$ by the size (in bytes) of each array element to get the distance from the beginning of the array to the $i$th element; add this distance to the memory address of the beginning of the array
## Register Allocation
- The purpose of **register allocation** is to determine which variables should be stored in which register
- It is necessary since the CPU has a fixed number of registers, but a proc can use an arbitrary number of variables
- For efficiency, we want as many variables as possible in registers, rather than in stack offsets
- One strategy: greedily allocate variables to registers on a first-come first-served basis, until we run out of registers
	- However, it turns out that in many cases, multiple variables can *share* the same register
- To determine when variables can be shared, we define the notion of liveness:
	- A variable $v$ is **live** at a program point $p$ if the value stored in $v$ at $p$ may be read from $v$ sometime after executing $p$
	- Given a variable $v$, the **live range** of $v$ is the set of all program points $p$ at which $v$ is live
	- Two variables can share the same registers if their live ranges are disjoint
- A variable is not live, rather it is dead if:
	1. A variable is dead if it will never be read, if there are no reads of the variable after a current program point
	2. A variable is dead if its current value will be overwritten before it is read, if every execution path from the current program point to a read of the variables passes through a write of the variable
- After computing live sets at each program point, information can be summarized in an **interference graph** 
	- Has a vertex for each variable, and an edge connecting a pair of variables if they **interfere**
- Then, a valid register allocation corresponds to a **colouring** of the interference graph
	- Assignment of colours to the vertices of a graph such that every edge connects two vertices with different colours
	- Colours correspond to edges, we want a minimal colouring corresponding to a register assignment using the minimum number of registers
# Chapter 5 - Procedures
- A **procedure** is an abstraction that encapsulates a reusable unit of code
- The code that calls a procedure (the **caller**) transfers control to the code of the procedure (the **callee**) by modifying the program counter.
- The caller may also pass **arguments** for the **parameters** of the procedure
- When the code of the procedure finishes executing, control transfers back to the instruction after the call of the procedure; the procedure may return a value back to the caller
- Conventions for procedures:
	- **Caller-save**: registers that may change their value during a procedure call
	- **Callee-save**: registers that must keep their original value after a procedure call
## Transferring Control
```scala
evaluate arguments into temporary variables 
Stack.allocate(parameters) // allocate space for all the params
parameter 1 := temporary 1 // copy in from temps to args
...
parameter n := temporary n 
LIS(Reg.targetPC (8)) // load the address of the proc
Use(proc)
JALR(Reg.targetPC (8)) // saves the current PC into Reg.link, transfer control to proc
```
## Prologue/Epilogue
```scala
Define(proc) // mark the address of the beginning of the proc
Reg.savedParamPtr (5) := Reg.result (3) // save param ptr of frame allocated in transferring control
Stack.allocate(frame) // push a chunk large enough to hold local vars in proc
dynamicLink := Reg.framePointer (29) // set dynamic link to FP of caller (offset from Reg.result)
Reg.framePointer (29) := Reg.result (3) // save addr of allocated frame into FP
savedPC := Reg.link (31) // save Reg.link to be able to return control (offset from FP)
paramPtr := Reg.savedParamPtr (5) // set paramPtr to access params (offset from FP)

... (body of procedure) ...

Reg.link (31) := savedPC // reset Reg.link to the savedPC
Reg.framePointer (29) := dynamicLink // reset the FP to the dynamic link
Stack.pop // pop proc frame (local vars)
Stack.pop // pop parameters
JR(Reg.link (31)) // return control to the calling code
```
## Summary of Procedure Call Conventions
- The stack pointer (register 30) and frame pointer (register 29) are callee-save: they are preserved by a procedure call.
- All other registers are caller-save: they may be modified by a procedure call.
- The calling code allocates the chunk to hold the arguments passed to the procedure. It provides the address of this chunk to the procedure in register 3, Reg.result. The procedure pops the chunk that holds the arguments before it returns to the calling code.
- The procedure allocates its own frame and pops it before it returns to the calling code.
![[Pasted image 20231217115223.png]]
## Implementing Variable Access
- Local variables are stored at constant offsets in the frame and can be accessed at constant offsets from the frame pointer
- To access a parameter, assembly language code needs to first read the paramPtr variable from the frame of the procedure to obtain the address of the chunk containing the parameters
	- It can then read/write the parameter at a constant offset from that address
# Chapter 6 - More Procedures
## Nested Procedures
- Want to be able to nest procedures inside of other procedures
### Static and Dynamic Scope
- **Dynamic scope** is a scope in which variables are scoped based on the execution of the program: can access variables declared only in the immediate procedure, or in procedures that called it, dynamically
- **Static scope** is a scope in which variables are scoped based on the test of the program: can access variables declared only in the immediate procedure, or in the procedures that wrap it, lexically
- Most languages use static scope because it is easier to reason with; we will use it too
### Implementing Nested Procedures
- **Dynamic link** points to the frame of the caller
- **Static link** points to the frame of the directly enclosing procedure; enables nested procedures
- Ex)
```scala
def f() = {
	val w = 5
	def g() = {
		val w = 7
		h()
	}
	def h() = {
		val z = 4
		z + w
	}
	g()
}
```
- Corresponding stack:
![[Pasted image 20231217122936.png]]
### Accessing Outer Variables
- The **nesting depth** is of a procedure is the number of levels of outer procedures that enclose it
- To generate the code to access a variable of an outer enclosing procedure:
	1. Calculate the difference in nesting depth, $n = depth(c) - depth(d)$ where $c$ is the procedure that is currently being executed/compiling, and $d$ is the procedure where the variable is declared in the program text
	2. Repeat the following steps $n$ times ("reading the static-link $n$ times")
		1. Read the param ptr of the current frame
		2. Read the static link in the current param frame, find frame of directly enclosing
	3. Access the desired variable in the resulting frame
### Computing Static Links
- The static link should always point to a frame of the procedure, $enclosing$, that directly encloses the procedure, $callee$
	- $depth(enclosing) = depth(callee) - 1$
- Define $n$ to be the number of times the caller needs to read a static link, starting from its own frame, to reach a static link that points to a frame of enclosing
	- $n = depth(caller) - depth(enclosing)$
	- $n = depth(caller) - (depth(callee) - 1)$
	- $n = depth(caller) - depth(callee) + 1$
- When $n=0$:
	- Don't need to read the static link at all; simply pass the frame pointer of the caller to become the static link of the callee
- When $n=1$:
	- Read the static link of the caller once, and it becomes the static link of the callee
- When $n>1$:
	- Read the static link n times, moving outwards in the program starting from the caller procedure, to obtain the value of the static link of the callee procedure
- When $n < 0$?
	- Invalid
## Function Values
- It is useful to allow functions to be values, so that they can be stored in variables and passed into/returned from procedures in the same way as other values
- Such **function values** are also called **first-class functions** to emphasize that anything that can be done with any other value can be done with a value that is a function
```scala
def procedure(x: Int) = { x + 1}
var increase: (Int)=>Int = procedure
// increase is now a variable that holds a function value
```
- Use Scala syntax for an **anonymous function**: the syntax $p => e$ denotes a function that takes a parameter $p$ and whose bode is the expression $e$
	- Another name is **lambda**
### Free Variables
- A variable is a **free variable** if it is not **bound** to anything
```scala
increase = { x => x + increment } // increment is free; invalid!

def increaseBy (increment: Int): (Int)=>Int = 
	{ x => x + increment } // increment is now bound to the param
increase = increaseBy(3)
```
### Implementing Function Values
- We will implement a function value using a **closure**, which is a pair of a *function pointer* and an *environment*
	- **Function pointer** is the address of the code of the body
	- **Environment** is a mapping from the free variables in the function body to their values
### Closure Operations
- There are two operations associated with closures:
	1. **Closure creation** which creates a closure value from a procedure
	2. **Closure call** which invokes the function represented by a closure value
```scala nums
def increaseByLacs(increment: Int): (Int)=>Int = {
	def procedure (x : Int ) = x + increment
	procedure // closure creation
}
increase = increaseByLacs(3)
increase(5) // closure call
```
#### Implementing a Closure Creation
- We must compute the two addresses that go into the new closure
- The function pointer is just the code address (label) of the procedure from which we are creating the closure
- The environment should be the address of the frame of the procedure directly enclosing the procedure from which we are creating the closure
	- This is the same address that we would pass as the static link if we were directly calling the proc instead of creating a closure out of it
	- Thus, environment computed just like we would compute the static link
#### Implementing a Closure Call
- Need to make use of the two addresses in the closure
- The code address tells us where the code of the function is, ie. the address that we load into the PC using JALR
- The environment provides values for free variables of the function value
	- Code of the function accesses these free variables using its static link, so must pass the environment address retrieved from the closure as the static link for the function that we call
#### Similarity with Regular Proc Calls
- Recall that a direct procedure call involves the following steps:
	1. Evaluate the arguments
	2. Compute the static link
	3. Allocate memory for the arguments and static link, and copy them from temporary variables into that memory
	4. Determine the address (label) of the code of the procedure to be called
	5. Set the program counter to that address using the JALR instruction
- Closure creation performs steps 2 and 4
- Closure call performs steps 1, 3, and 5 (reading the addresses that would normally come out of steps 2 and 4 out of the closure value)
### Extent of Environments
- The presence of function values make stack no longer a viable option to always allocate local variables; the function may access the variable at a later time, when it is called by closure call -> the extent is longer than the execution of the function
- A **heap** is necessary to allocate memory for the parameters and frames of procedures for some procedures
	- Necessary for procedures whose variables could be accessed by a procedure that is made into a function value (i.e., is mentioned at a closure creation site)
- If a procedure $p$ is made into a function value, we can designate all outer enclosing procedures of $p$ to be allocated on the heap, because the body of $p$ could potentially access their variables
- Also, every procedure from which we create a function value (i.e., every procedure mentioned at a closure creation site) also has its frame and parameters allocated on the heap
### Closure Representation
- We allocate memory for a Chunk with two variables: the two addresses needed for a closure
- We use the address of this memory as the representation of the closure value, which we can store into a variable or pass into/return from a procedure
## Objects
- Many OOP languages allow the creation of **objects**: records that contain both data (state) and procedures (behaviours)
- Each object of a given class has its own state
- The object is a collection of function values with a common shared environment, which the function values can use to maintain and communicate state with each other
- Function values have enough expressive power to implement objects
## Tail Calls
- A **tail call** is a call that is executed as the last operation before a procedure returns
	- It doesn't need to be textually at the end of the code of a procedure because it can be wrapped in one or more control structures
- In the presence of a tail call, we can safely *shorten* the extent of the variables of the procedure so that it ends just *before* the tail call, instead of immediately after it
	- If any variables are used in arguments to the tail call, the extent ends after the arguments have been evaluated but just before we call the callee
	- With shortened extends, we can deallocate the frame of the caller immediately before the tail call rather than immediately after it
	- This reduces the number of frames on the stack at any given time
### Implementing Tail Call Optimization
- To optimize a tail call to free the frame of the caller before the call, we need to interleave the implementation of the call with the implementation of the epilogue of the caller
- Pseudocode:
```txt nums {3}
// (tail) call 
evaluate arguments 
// 
allocate memory for callee’s parameters 
write argument values into callee’s parameters 
LIS/JALR 

// caller’s epilogue 
Reg.link = savedPC 
Reg.framePointer = dynamicLink 
Stack.pop // caller’s frame 
Stack.pop // caller’s parameters 
JR(Reg.link (31))
```
- Want to interleave the epilogue in place of the highlighted line 3
```txt nums {3-7}
// (tail) call 
evaluate arguments 
// caller’s epilogue 
Reg.link = savedPC 
Reg.framePointer = dynamicLink 
Stack.pop // caller’s frame 
Stack.pop // caller’s parameters 
allocate memory for callee’s parameters 
write argument values into callee’s parameters 
LIS/JALR 

JR(Reg.link (31))
```
- Problem 1: LIS/JALR immediately followed by JR(Reg.link)
	- The JALR will overwrite the intended return address that was placed in Reg.link = savedPC
	- Since callee called by JALR instr returns to the JR(Reg.link) using its own JR(Reg.link instr at the end of the callee, we can elliminate the extra step and make the callee's JR(Reg.link) return directly to where we wanted the caller's JR(Reg.link) to return to
		- Replace JALR by JR: the address we pass into the callee in the link register 31 will directly be the addr that the caller should return to, rather than the addr of the JR(Reg.link) instr in the caller
```txt nums {10}
// (tail) call 
evaluate arguments 
// caller’s epilogue 
Reg.link = savedPC 
Reg.framePointer = dynamicLink 
Stack.pop // caller’s frame 
Stack.pop // caller’s parameters 
allocate memory for callee’s parameters 
write argument values into callee’s parameters 
LIS/JR
```
- Problem 2: argument values to be stored in callee's parameters are in temp vars in the caller's frame, which is popped off the stack just before
	- Memory locations of these temp vars are reused for the callee's parameters and there is a risk that the values of some of the temp vars will be overwritten before we copy them to the corresponding params
	- Fix by allocating a temporary copy of the callee params first before popping the caller's frame from the stack
```txt nums {3-4, 11-12}
// (tail) call 
evaluate arguments 
allocate memory for temporary parameters 
write argument values into temporary parameters 
// caller’s epilogue 
Reg.link = savedPC 
Reg.framePointer = dynamicLink 
Stack.pop // temporary parameters
Stack.pop // caller’s frame 
Stack.pop // caller’s parameters 
allocate memory for callee’s parameters 
copy temporary parameters into callee’s parameters
LIS/JR
```
 - Diagram of the stack:
![[Pasted image 20231217142535.png]]
- Still risky because it copies values from the temp params to the callee's params after the temporary params have already been popped off the stack
	- Must ensure not to use the stack for any other purpose between these two operations
# Chapter 7 - Scanning and Regular Languages
## Formal Languages
- **Formal languages** describes sets of strings with mathematical rigour
	- Strings are programs or parts of programs
	- We will define the sets of strings that are valid programs in a particular programming language
- Terminology
	- An **alphabet** is any *finite* set of **symbols**
		- Ex) {0, 1}, {0, ..., 9}, {A, ..., Z, a, ..., z}
	- A **word** over some given alphabet is a **finite** sequence of symbols drawn from that alphabet
		- Ex) 123 is a word over the alphabet of decimal digits, abc is a word over the alphabet of letters
		- A word with zero symbols is called the empty word, usually denoted as ε
	- A (formal) **language** is a set of words over some alphabet
		- Ex. the set of binary strings of length 32 (MIPS words), the set of prime numbers written in decimal, the set of valid Scala programs
- We can classify a language into one of a number of **language classes** depending on properties of the language
	- **Finite languages** are *finite* sets of words
	- **Regular languages** are sets of words that can be specified using the techniques that explored in this module
	- **Context-free languages** are sets of words that can be specified using more powerful techniques that will be discussed in the next module
	- **Decidable languages** are sets of words for which there exists an algorithm that decides membership; ie. decides whether or not some given word is in the set
- Compiler will use a *regular language* for the process of **scanning**: partitioning an input program (a string of characters) into a sequence of key-words, symbols, identifiers, and literals, collectively called **tokens**
- We will use *context-free language* to recognize the structure of the program; process is called **parsing**
- In general, want to use *decidable languages* to specify valid programs, since we want compiler to be able to determine if an input program is valid
	- All *regular* and *context-free languages* are *decidable*
## Deterministic Finite Automata
- To specify the scanning process, will use **deterministic finite automata (DFAs)**
	- A directed graph whose nodes are called **states** and whose edges are called **transitions**
	- Each transition is labelled with some symbol from the alphabet
	- One state of the DFA is identified as the **start state**
	- Some subset of the the states are **accepting states**
	- In a given state and for a given symbol, there can be at most one transition that originates from the state and is labelled with that symbol
- Determining if a string is in the language using a DFA:
	- Start in the *start state* of the DFA and consider each symbol in the input string, in order
	- For each symbol, follow the transition labelled with that symbol from the current state to a new state of the DFA
	- If there is no transition, then the DFA is said to *get stuck*, the word is not in the language
	- If it goes through the whole string without getting stuck
		- If ends up in an accepting state, the word is in the language
		- If ends up in a non-accepting state, the word is not in the language
- Example DFA that specifies the language of words of even length over the alphabet {a, b}:
![[Pasted image 20231217150832.png]]
### DFAs Formally
- A **deterministic finite automaton (DFA)** is a five-tuple $\langle \Sigma, Q, q_{0}, A, \delta \rangle$ where:
	- $\Sigma$ is a finite set of *symbols* (the alphabet)
	- $Q$ is a finite set of *states*
	- $q_{0} \in Q$ is the *start state*
	- $A \subseteq Q$ is the set of *accepting states*
	- $\delta : Q \times \Sigma \rightarrow Q$ is the *transition function* that, given a state $q$ and alphabet symbol $a$, returns the new state $q'$ to transition to after processing the symbol $a$ in the state $q$
- Given a DFA $\langle \Sigma, Q, q_{0}, A, \delta \rangle$, we can define the **extended transition function** $\delta^{\ast} : Q \times \Sigma ^{\ast} \rightarrow Q$ which maps a state $q$ and a sequence of symbols $w$ to a new state $q'$ as follows:
	- $\delta ^{\ast}(q, \epsilon) = q$
	- $\delta ^{\ast}(q, first::rest) = \delta ^{\ast}(\delta(q,first), rest)$
- A DFA **accepts** a word $w$  if $\delta ^{\ast}(q_{0}, w) \in A$ 
- We then define the **language specified by the DFA** as the set of words that the DFA *accepts*
- A language is **regular** if there exists a DFA that specifies it
- For a given language, there may be many different DFAs to specify it, but the minimal DFA is unique
## Non-Deterministic Finite Automata
- Extension of DFAs in which there are two paths from the same state for the same symbol, called **non-deterministic finite automata (NFA)**
- NFAs can also be used to specify a language
- For a given word $w$, there may be multiple possible paths through an NFA that end in different states
- We define an NFA to **accept** a word $w$ if *at least one* of the paths through the NFA following the symbols in $w$ ends in an accepting state
### NFAs Formally
- Can define NFAs similar to DFAs, with a 5-tuple $\langle \Sigma, Q, q_{0}, A, \delta \rangle$ whose components are the same as those of a DFA
	- Only difference is that the transition function $\delta : Q \times \Sigma \rightarrow 2^{Q}$ now maps a state $q$ and an alphabet symbol $a$ to a set of states $Q' \subseteq Q$
	- Notations $2^{Q}$ denotes the set of sets of states
- As with DFAs, can define an extended transition function $\delta^{\ast} : Q \times \Sigma ^{\ast} \rightarrow 2^{Q}$ that computes the set of states that the NFA can end up in if it starts in some state q and follows transitions corresponding to a sequence of symbols $w$:
	- $\delta ^{\ast}(q, \epsilon) = \{q\}$
	- $\delta ^{\ast}(q, first::rest) = \bigcup\limits_{q' \in \delta(q,first)} \delta ^{\ast}(q', rest)$
- The NFA **accepts** a word $w$ if $\delta ^{\ast}(q_0, w) \cap A \neq \{\}$
- The **language specified by the NFA** is the set of words that the NFA *accepts*
## Epsilon-Transitions
- To make it easier to systematically combine NFAs, we add **$\epsilon$-transitions**
- Whenever NFA is in a state with an outgoing $\epsilon$-transition, it may optionally choose to follow that transition without processing any symbols from the input word
- Using $\epsilon$-transitions allow us to combine two DFAs without having to modify them, just add a new state with $\epsilon$-transitions to the former start state of the two DFAs
![[Pasted image 20231217153728.png]]
## Regular Expressions
- **Regular expressions** are one or more ways of specifying the same languages that can be specified by DFAs and NFAs
- A regular expression over an alphabet $\Sigma$ is defined according to the following rules:
	- $\emptyset$ is a regex; its meaning is $L(\emptyset) = \{\}$ (empty set)
	- $\epsilon$ is a regex; its meaning is $L(\epsilon) = \{\epsilon\}$ (set with only the empty word)
	- For each symbol $a \in \Sigma$, $a$ is a regex; its meaning is $L(a)=\{a\}$ (set of just $a$)
	- Whenever $R_{1}, R_{2}$ are regex, $R_{1}\vert R_{2}$ is also a regex; its meaning is $L(R_{1} \vert R_{2}) = L(R_{1}) \cup L(R_{2})$ (the union of the sets of words denoted by $R_{1}, R_{2}$)
	- Whenever $R_{1}, R_{2}$ are regex, $R_{1}R_{2}$ is also a regex; its meaning is $L(R_{1}R_{2})=\{xy \vert x \in L(R_{1}), y \in L(R_{2}) \}$ (set of words that can be split into 2 parts, $x, y$, such that $x$ is in the set denoted by $R_1$, $y$ is in the set denoted by $R_{2}$)
	- Whenever $R$ is a regex, $R^{\ast}$ is also a regex; its meaning is $L(R^{\ast})=\{x_{1}x_{2}\dots x_{n} \vert n \geq 0, \forall i . x_{i} \in L(R)\}$  (set of words that can be split into zero or more parts $x_{1}x_{2}\dots x_{n}$ such that each of the parts $x_{i}$ is in the set denoted by $R$)
- **Kleene's theorem** tells us that DFAs, NFAs (with and without $\epsilon$-transitions), and regular expressions all have the same expressive power; for a language L, the following are equivalent:
	1. There exists a DFA that specifies L
	2. There exists an NFA without $\epsilon$-transitions that specifies L
	3. There exists an NFA with $\epsilon$-transitions that specifies L
	4. There exists a regular expression that specifies L
- Languages that can be specified by a DFA, NFA, or regular expression are called **regular languages**
## Scanning
- **Recognition** is determining if a given word $w$ is in the specified language
- **Scanning** is determining if given a word $w$, can $w$ be partitioned into subwords $w = w_{1}w_{2}\dots w_{n}$ such that each subword $w_{i}$ is in some given language $L$
- In practice, tokens are a pair of a **kind** and a **lexeme** 
	- Each accepting state in the DFA generating a particular kind of token - this is the kind
	- The lexeme is the actual string of characters that were scanned to make up the token
- We can state the **scanning problem** as:
	- The input is a word $w$ and a language $L$ of valid tokens
	- The output is a sequence of non-empty words $w_{1},w_{2},\dots , w_n$ such that:
		1. If we concatenate all the words in the sequence, we get back $w$: $w_{1}w_{2}\dots w_{n}=w$
		2. Each of the words is in the language of valid tokens: $\forall i . w_{i} \in L$
- The **maximal munch scanning problem** simply adds a third condition on the output:
	- Each $w_i$ is the longest prefix of the remainder of the input $w_{i}w_{i+1}\dots w_n$ that is in L
	- It requires a scanner at each step to greedily find the longest token possible before continuing
	- PRO: output is unique; only one way to scan any given input program
	- CON: might not have any solution in some cases where the original scanning problem does
	- CON: the intuition for which programs are valid is obscured
## Maximal Munch Scanner Implementation
- Algorithm to solve the maximal munch scanning problem:
	1. Run the DFA for $L$ on the remaining input until the DFA gets stuck or the end of input is reached
	2. If the DFA is in a non-accepting state, backtrack the DFA and the input to the last-visited accepting state; if no accepting state was visited, there is no solution, so output an error
	3. Output a token corresponding to the accepting state that the DFA is in
	4. Reset the DFA to the start state and repeat, starting again from Step 1
# Chapter 8 - Parsing
- Nested constructs such as expressions cannot be specified using regular languages, unless they are restricted to some fixed number of levels of nesting
- To specify nesting constructs, we need more expressive *context-free languages*
## Context-Free Grammars
- Context-free grammars define steps of breaking components down into smaller pieces
- Context-free grammars support recursion
- Given a context-free grammer, we can construct **parse trees** for words
- Formally, a **context-free grammar** is a 4-tuple $\langle V, \Sigma, P, S \rangle$ where:
	- $V$ is a finite set of **non-terminal symbols**
	- $\Sigma$ is a finite set of **terminal symbols** (the alphabet)
	- $P$ is a finite set of **productions**, also called *rules*
	- $S \in V$ is the **start non-terminal**

- Some conventions for the rest of this module:
	- variables $a, b, c, d$ always refer to terminal symbols in $\Sigma$ 
	- variables $A, B, C, D, S$ always refer to non-terminal symbols in $V$ 
	- variables $W, X, Y, Z$ always refer to terminal or non-terminal symbols in $\Sigma \cup V$
	- variables $w, x, y, z$ always refer to sequences of terminal symbols from $\Sigma$, i.e., to words
	- variables $\alpha, \beta, \gamma, \delta$ always refer to sequences of terminal or non-terminal symbols from $\Sigma \cup V$
## Derivations
- Whenever $A \rightarrow \gamma \in P$  is a production in the grammar, we say that $\alpha A \beta$  **directly derives** $\alpha \gamma \beta$, and we write $\alpha A \beta \Rightarrow \alpha \gamma \beta$ , for all non-terminals $A \in V$ and all sequences of terminal and non-terminal symbols $\alpha, \beta, \gamma$
- Whenever $\alpha_{1} \Rightarrow \alpha_{2} \Rightarrow \dots \Rightarrow \alpha_{n}$ is a sequence of zero or more *directly-derives* steps, we say that $\alpha_{1}$ derives $\alpha_{n}$ and we write $\alpha_{1}\Rightarrow^{\ast}$ $\alpha_{n}$ 
	- The sequence of *directly-derives* steps used to reach $\alpha_{n}$ from $a_{1}$ is called a **derivation**
- We say that the **language generated by the grammar** $G = \langle V, \Sigma, P, S \rangle$ is $L(G) = \{w \in \Sigma^{\ast} \vert S \Rightarrow^{\ast} w\}$ 
	- The languages contains all the words $w$ that are derived by the start symbol $S$ of the grammar
	- Sometimes we use the word **specified** instead of *generated*: $w$ is specified as being in $\Sigma^{\ast}$; it must be a sequence of terminal symbols only, and cannot contain any non-terminal symbols
- A **language is context-free** if there exists a context-free grammar that generates it; there may be multiple grammars that generate the same language
	- It is undecidable, given two context-free grammars $G_{1}$ and $G_{2}$ whether they generate the same language
	- For regular languages, on the other hand, it is decidable
	- Thus, moving to context-free languages, we gain more *expressiveness*, but lost some of the ability to *reason* about the properties of the languages
- A context-free grammar is *ambiguous* if we can use it to construct two distinct parse trees for the same word
## CYK Parsing
- For a given $\alpha, x$ how do we decide whether $\alpha \Rightarrow^{\ast} x$? Use an algorithm:
```scala nums
parse(α, x) = { // does α ⇒∗ x ?
	if (α.isEmpty) { // Case 1
		if (x.isEmpty) true // the empty sequence derives itself only
		else false 
	} else if (α = aβ) { // Case 2  (a is terminal, β is rest)
		if(x = az && parse(β, z)) true // x must start with a, rest must parse
		else false 
	} else if(α = A) { // Case 3 (A is single non-terminal)
		for each production A → γ ∈ P { // try all derivations of A
			if(parse(γ, x)) return true // if a derivation γ derives x 
		} 
		false // fails if no derivation of A derives x
	} else { // α = Aβ Case 4 (A is single non-terminal, β is non-empty rest)
		for each way to split x into two substrings x = x1x2 { 
			// try all ways to split x (with x1, x2 possibly empty)
			// check if parses A ⇒∗ x1, β ⇒∗ x2
			if (parse(A, x1) && parse(β, x2)) return true 
		} 
		false // fails if no split works
	}
}
```
### Memoization
- Memoization consists of keeping track of the questions the algorithm has already been asked in a table
- If a question is ever repeated, instead of recomputing the answer, the memorized algorithm simply looks it up in the table and returns the answer that it returned the first time the question was asked
- It is inefficient to use $x$ as part of the key (sequence of terminal symbols that is $O(n)$ long in the worst case) - better: since every $x$ is a subset of an input $w$, keep track of $x$ as two integers (the start index in $w$, length)
- Modify the algorithm with the following:
	1. At each place where the algorithm returns a value, true or false, add that value to the memoization map under the key derived from the values of $\alpha$ and $x$
	2. At the beginning of parse, first check whether the memoization map is already defined for the current values of $\alpha$ and $x$; if yes, just return the value from the map and skip the rest 
	3. Also at the beginning of parse, if not already defined and before beginning the computation, add the value `false` to the map under the current values of $\alpha$ and $x$
		- Ensures that if we encounter the same question again, will return `false` to break the infinite cycle
		- Once it reaches an answer, will overwrite the map, so any later queries about it will return `true`
### Time and Space Complexity
- The most significant data structure that the algorithm builds is the memoization table
- Since each key is a triple of $\langle \alpha, start, length \rangle$, with $start$ and $length$ ranging from 0 to the length of the input string $w$, they can each have $O(n)$ possible values, where $n$ is the length of $w$
	- Thus, total space complexity is bounded by $O(n^2)$
- Each execution of the main body, not counting time spent in recursive calls can take up to $O(n)$ time because of the loop in Case 4 (number of loops in Case 3 is constant for a given grammar, Case 1/2 constant)
- Each execution also adds an entry to the memoization table, bounded by the number of table entries $O(n^2)$
	- Thus, overall running time of the algorithm is $O(n^3)$, where $n$ is the length of $w$, the string to be parsed
### Constructing the Parse Tree
- Algorithm currently only decides if a derivation $\alpha \Rightarrow^{\ast} x$ exists, want to modify it to construct a parse tree
- In every place where it returns `true`, it needs to instead return a sequence of parse trees
- Return type is a *sequence* because $\alpha$ is a sequence of symbols; parse will return one parse tree for each symbol in $\alpha$, with the symbol at the root of the tree 
- What it should return for each of the cases:
	1. Case 1, since $\alpha$ is empty, the empty sequence of parse trees must be returned
	2. Case 2, the sequence of parse trees starts with a parse tree showing that $\alpha \Rightarrow^{\ast} \alpha$, followed by a sequence of parse trees showing that $\beta \Rightarrow^{\ast} z$ 
		- Creates a leaf node with just $\alpha$, prepends it to sequence of parse trees returned by recursive call parse($\beta , z$)
	3. Case 3, the algorithm creates a new node with symbol $A$ to represent the derivation step $A \Rightarrow \gamma$; trees returned by the recursive call to parse($\gamma, x$), with the symbols of $\gamma$ as their roots, are used as the children of the new node
	4. Case 4, the algorithm concatenates the two sequences returned by the two recursive calls to parse($A, x_{1}$) and parse($\beta, x_{2}$)
## Other Parsing Algorithms
- Advantages of CYK algorithm are that it can be understood intuitively and by systematically considering the possible derivations in the various cases; it also works with *any* context-free grammar
- Disadvantage is its $O(n^3)$ running time - fine for our purposes, not sufficient for industrial size algorithms
- **LR(k) and LL(k)** are classes of parsing algorithms that are popular in practice due to their guaranteed $O(n)$ running time
	- Disadvantage is that can only parse certain classes of grammars, complex and hard to understand in full
	- Known to be unambiguous
- **Earley's** parsing algorithm can parse with *any* context-free grammar
	- Worst-case running time is $O(n^3)$ for ambiguous grammars, $O(n^2)$ for unambiguous grammars, and $O(n)$ for a class of grammars that includes almost all LR(k) and LL(k) grammars
## The Correct Prefix Property
- When the input is *not* a word in the language, want a detailed error message indicating why it could not be parsed
- One such feedback is an indication of the position in the input at which the parser got stuck; precisely the parser returns the longest prefix of the input that is also a prefix of some word in the language
- Such a prefix is *correct* in the sense that, if it is completed with the right ending, it can become a word in the language
- Formally, suppose:
	1. $w$ is the input to a parser
	2. $w$ can be decomposed as $w = xaz$, where $x$ and $z$ are sequences of terminal symbols and $a$ is a terminal symbol
	3. there exists some sequence $y$ of terminal symbols such that $xy$ is a word in the language
	4. there is no sequence $y'$ of terminal symbols such that $xay'$ is a word in the language
- In this case, $x$ is the longest correct prefix and $a$ is informally the position of the bug
- A parser has the **correct prefix property** if when parsing input $w = xaz$, it reports an error at the position of $a$, enabling the user to locate the bug in the input
- Earley's parsing algorithm and the LL(k) and LR(k) parsing algorithms have the correct prefix property, but the CYK parsing algorithm does not
# Chapter 9 - Context-Sensitive Analysis and Types
- The purpose of **context-sensitive analysis** is to enforce requirements on the program, as well as to compute information that will be needed by the compiler to generate machine-language code
- For Lacs, the context-sensitive analysis needs to perform 2 main tasks:
	1. Needs to resolve identifiers to the variables or procedures they refer to; can be done by creating a symbol table for each scope in the Lacs program and mapping each name declared to either a variable declaration or procedure declaration, may report one of 2 errors:
		1. The analysis detects uses of identifiers that have not been declared in a variable or procedure declaration
		2. The analysis detects duplicate declarations, multiple declarations of the same name in the same scope
	2. Compute a type for each expression and subexpression in the program and check that the program obeys the Lacs typing rules
## Types
- Three definitions of **types**:
	1. A type can be defined as a collection of values, such as integers, characters, strings, programs, trees, sounds, images, videos, etc (values in this context are the high-level concepts we express in a programming language)
	2. A type is an interpretation of sequences of bits
	3. A type is a computable property of programs that guarantees some property about their execution
## Types in Lacs
- Lacs has two sorts of types:
	1. Int is a type whose values are the integers in the range \[$-2^{31}$, $2^{31}-1$\]; values of this type support operations that implement arithmetic modulo $2^{32}$
	2. If $\tau , \tau_{1},\tau_{2},\dots, \tau_{n}$ are types, then $(\tau_{1},\tau_{2},\dots, \tau_{n})=> \tau$ is a type whose values are procedures that take $n$ arguments of the corresponding types $\tau_{1},\tau_{2},\dots, \tau_{n}$ and return a value of type $\tau$
		- A value of such a proc supports the proc call operation
## Type Systems
- A **type system** is a collection of *inference rules* that define a *typing relation*
- A **typing relation** is a set of triples $\langle \Gamma, E, \tau \rangle$ 
	- The $\Gamma$ is typically called a **typing context** in the terminology of typing systems and corresponds to a *symbol table* in our compiler; symbol table maps each identifier to its meaning, a declaration of that identifier in the program
	- The $E$ is typically called a **term** in the terminology of type systems and corresponds to a subtree of the *parse tree* in our compiler
	- The $\tau$ is a *type*
- The presence of a triple $\langle \Gamma, E, \tau \rangle$ in the typing relation is intended to mean that the expression represented by the tree $E$ has type $\tau$, using the symbol table $\Gamma$ to look up the declarations of any identifiers used in $E$
- Notation: $\Gamma \vdash E : \tau$ to mean $\langle \Gamma, E, \tau \rangle \in T$ 
- An **inference rule** allows us to specify the typing relation $T$ when it is an infinite set; it is a collection of zero or more **premises** and a **conclusion**
	- Premises are written above a horizontal line; conclusion below
	- The rule asserts that *if* the premises hold, *then* the conclusions hold
- These statements are written using **metavariables**; each metavariable stands for many possible typing contexts, parse trees, or types - they are implicitly universally quantified
![[Pasted image 20231218111228.png]]
## Type Soundness
- We say that a type system is **sound** whenever the expression of tree $E$ has type $\tau$ in the typing relation, then during the execution of a program, the expression $E$ evaluates to a value of type $\tau$
- The Lacs typing rules were designed with the intent of being unsound
## Well-Formedness Relation
- Type inference rules do not yet give types to all Lacs parse trees; give types only to trees that represent expressions, which evaluate to values
- Some Lacs parse trees represent parts of a program that are not expressions and do not evaluate to a value; ex) procedure declaration
- We want inference rules to check that the procedure declaration declares a Lacs procedure and that it does not contain type errors in its body
- To perform such checks, we define another relation, $W$, called the **well-formedness relation**; it is a set of pairs $\langle \Gamma, E \rangle$ where $\Gamma$ is a typing context (symbol table) and $E$ is a term (parse tree)
- The presence of a parse tree $E$ in the well-formedness relation $W$ means that the tree is considered to be a valid Lacs parse tree with no compile-time errors in it, but that it is not an expression and therefore it does not make sense to assign it any particular type
- Notation: $\Gamma \vdash E$ means $\langle \Gamma, E \rangle \in W$
- Well-formedness relation uses the same style of inference rules as for the typing relatio
- These rules enforce the compile-time requirements of Lacs programs for parts of the parse tree other than expressions
![[Pasted image 20231218112858.png]]
- Program
	- Premise requires that every procedure declaration in the program must be well-formed, which will be by next inference rule
	- Symbol table is used to check well-formedness of each procedure is the list of all (outermost) procedure declarations in Lacs
	- Basically just checks each outer procedure in the program for well-formedness
- Procedure declaration
	- The premises of the rule first enforce the requirement that the parameters, variables, and nested procedures must all have different names
	- The symbol table of $\Gamma$ is extended with the declarations of the parameters, the variable declarations, and the nested procedure declarations
		- Commas in notation say that the new declarations take precedence over the declarations of the same name in $\Gamma$ (ie. replace)
	- The body is type-checked using the premise on the bottom right with the extended symbol table (this is type-checking rather than well-formedness)
	- Each of the nested procedures are checked recursively for well-formedness on the bottom left
## Type Checking
- Given a $\Gamma$ and $E$, find a s $\tau$ such that $\langle \Gamma, E, \tau \rangle \in T$
- Observe that the triple can only be in $T$ if the conclusion of some type inference rule forced it to be there
- All of the Lacs type inference rules, in their conclusions, they all have different syntactic forms
	- For each of the Lacs grammar rules that could be the production rule expanding the root not of a tree $E$, there is only one inference rule that could possibly apply
	- Thus, our implementation can look at the grammar production rule in the root node of the tree $E$ and implement the inference rule corresponding to that production rule
- In implementing an inference rule, the type checker needs to determine values for the metavariables from the tree and the symbol table
- With values for the metavariables, the type checker checks the premises of the inference rule (in many cases involves recursive calls to determine types for subtrees of $E$)
- Finally, it returns the type specified by the conclusion of the inference rule
- For each inference rule, the Scala code proceeds from the bottom left, from the syntactic shape of the tree in the conclusion of the rule, to the top of the rule, where the premises are checked, and finally to the bottom right, where the inference rule specifies the type to be returned, as shown in the following diagram:
![[Pasted image 20231218114852.png]]
# Chapter 10 - Overall Compiler Structure
- In a compiler, an **intermediate representation (IR)** is a data structure that represents the program being compiled
- Many compilers have multiple intermediate representations and multiple translation passes between them to *gradually* transform a source-level view of the program into the final machine language code as a sequence of multiple transformation steps
- Following diagram summarizes the overall structure of a compiler and of the Lacs compiler in particular:
![[Pasted image 20231218120013.png]]
# Chapter 11 - Heap Memory Management
- We observed that function values in functional languages and objects in object-oriented languages require more flexible extents: need to use a **heap**
## Heap with Explicit Deallocation
- Need to create additional data structures to track which blocks of memory are in use (allocated) and which blocks have been freed for reuse (deallocated)
- Since the blocks already contain the size, it is easy to iterate through them
- Need to keep another bit in each block to indicate if it has been allocated or free for reuse
- To allocate a new block of a given size, simply iterate through the list of blocks in heap until find one that is large enough and bit indicates it is free
	- Downside: finding a free block requires linear search through all the blocks

- Can make it more efficient by organizing all the free blocks in the heap into a linked list
	- Then, to allocate, only need to search blocks in the list (which are all free) rather than the entire heap
	- This implementation is adapted from the implementation of malloc in C
![[Pasted image 20231218121622.png]]
- Initialize the heap with two blocks:
	- A dummy block with no usable space, containing only the two words needed for its header (ensures every real block has some block before it)
	- Second block is a free block consisting of the entire remainder of the heap
- The *next* field of dummy block always points to the beginning of the linked list of free blocks
- The free blocks are linked using their *next* fields; next of the last free block points to the memory location immediately after the end of the heap to indicate end of linked list
- Malloc implementation:
```scala nums
def malloc(wanted) = { 
	def find(previous) = { 
		val current = next(previous) 
		if (size(current) < wanted + 8) find(current) 
		else { 
			if (size(current) >= wanted + 16) { // split block 
				val newBlock = current + wanted + 8 
				setSize(newBlock, size(current) - (wanted + 8))
				setNext(newBlock, next(current)) 
				setSize(current, wanted + 8) 
				setNext(previous, newBlock) 
			} else { 
				// remove current from free list 
				setNext(previous, next(current)) 
			} 
			current 
		} 
	} 
	find(heapStart) 
}
```
- The heap after executing `a = malloc(8)` with this implementation:
![[Pasted image 20231218122056.png]]
- The heap after also executing `b = malloc(8)` and `c = malloc(8)`:
![[Pasted image 20231218122138.png]]
- Use the following implementation to free a block whose address is passed for the parameter:
```scala nums
def free(toFree) = { 
	def find(previous) = { 
		val current = next(previous) 
		if (current < toFree) find(current) 
		else { 
			val coalesceWithPrevious = 
				previous + size(previous) == toFree &&
				previous > heapStart
			val coalesceWithCurrent = 
				toFree + size(toFree) == current &&
				current < heapStart + heapSize 
			if (!coalesceWithPrevious && !coalesceWithCurrent) {
				setNext(toFree, current)
				setNext(previous, toFree)
			} else if (coalesceWithPrevious && !coalesceWithCurrent) {
				setSize(previous, size(previous) + size(toFree)) 
			} else if (!coalesceWithPrevious && coalesceWithCurrent) { 
				setSize(toFree, size(toFree) + size(current))
				setNext(toFree, next(current)) setNext(previous, toFree) 
			} else { // coalesceWithPrevious && coalesceWithCurrent
				setSize(previous, size(previous)+size(toFree)+size(current)) 
				setNext(previous, next(current))
			}
		}
	} 
	find(heapStart)
}
```
- Searches through the linked list of free blocks to set previous to the last block before the block toFree to be freed, and current to the block immediately after previous in the free list
- Issues arise when freeing adjacent blocks, won't see that there is a large space free
![[Pasted image 20231218124609.png]]
- We need to **coalesce** a freed block by combining it with the blocks immediately before and after it in memory, if those blocks are also free
- There are 4 cases of coalescing, summarized below (black arrows/boundaries indicate the state before the block toFree is freed and coalesce, the read arrows and crossed-out black boundaries indicate the state after the block toFree is freed and coalesced)
![[Pasted image 20231218124933.png]]
## Fragmentation and Compaction
- Supposed instead of freeing two adjacent blocks, we free two blocks that are separated by another block
- Now, there is technically enough space to allocate a block of a larger size, but the heap space if **fragmented**: it is split into two small free blocks that cannot be coalesced because they are separated by the middle block, which is in use
![[Pasted image 20231218125147.png]]
- To avoid fragmentation in all cases, we need to be able to *move* blocks in memory
- In the example, we could combine the two free blocks into one if we could move block b to the beginning or end of the heap 
- The process of moving blocks that are in use together, so they are adjacent in the heap with no fragmented free blocks between them is called **compaction**
- If compaction moves a block, the address of the block changes; any pointers in the heap that hold the original address need to be updated to the new address to maintain the structure of the data in the heap
	- Only possible if we can identify all the pointers in memory; thus compaction requires *sound type information*
- Implementing compaction in the heap allows allocation and deallocation to be simplified considerably:
	- Allocation
		- No longer need to maintain and search a list of free blocks to be reused
		- Instead, it can always return the block after the last one that was allocated
		- When allocation reaches the end of the heap, it can trigger a compaction, which will move all the blocks that are in use together, leaving one large area of free memory after them that can again be used to allocate more blocks simply by incrementing a pointer like in the simple heap allocator
	- Deallocation
		- No longer need to link freed blocks into a linked list or to coalesce them
		- Needs to only mark blocks as free for a later compaction pass
- Although each compaction may take a long time, it is done very rarely, so the cost can be spread over many allocations => cost per allocation is low (many cases, lower than the cost of work done by malloc and free)
## Automatic Garbage Collection
- Instead of the program explicitly deallocating blocks for reuse, design compaction algorithm to compact all blocks that are reachable by a path of pointers from the stack
- If a block is not reachable by such a path, then the executing program can never find its address and will therefore never access it so it is free
- A memory manager that finds free blocks automatically in this way is an **automatic garbage collector**
- A block in the heap is **reachable** if:
	1. The address of the block is in a block on the stack, or
	2. The address of the block is in an already reachable block in the heap
### Cheney's Copying Garbage Collector
- Cheney's algorithm splits the heap into two halves, called **semispaces**
	- The two semispaces are the **from-space** and the **to-space**
- New blocks are allocated in the from-space; the heapPointer keeps track of the first free address in the from-space, so allocation only increments this pointer
- When the heapPointer reaches the end of the from-space, a compaction is triggered, copying all *reachable* blocks from the from-space to the beginning of the to-space
- After this is done, the designations of the from and to spaces are switched; the heapPointer is set to the address in the new from-space after all the blocks have been copied

- We have three constants to help initialize the heap (assuming an even number of words in heap):
	- `heapStart`, the address of the first word in the heap (1/4 of memory)
	- `heapEnd`, the address of the last word in the heap (3/4 of memory)
	- `heapMiddle`, the address halfway between `heapStart` and `heapEnd` (1/2 of memory)
- Designate two registers to implement the heap:
	- `heapPointer`, holds the address of the first free word in the from-space
	- `fromSpaceEnd`, hold the address after the last word in the from-space

- Initializing the registers:
```scala nums
def init() = {
	heapPointer = heapStart
	fromSpaceEnd = heapMiddle
}
```

- Allocating a block of wanted bytes:
```scala nums
def allocate(wanted) = { 
	if (heapPointer + wanted > fromSpaceEnd) { 
		gc()
	} 
	val oldHeapPointer = heapPointer 
	heapPointer = heapPointer + wanted 
	oldHeapPointer 
}
```

- Compaction happens in the gc() method:
```scala nums
def gc() = { 
	val toSpaceStart = if (fromSpaceEnd == heapMiddle) heapMiddle 
					   else heapStart 
	var free = toSpaceStart
	var scan = dynamicLink // top of stack, not incl. frame of gc 
	while(scan < maxAddr) { 
		forwardPtrs(scan)
		scan = scan + size(scan)
	} 
	scan = toSpaceStart 
	while (scan < free) {
		forwardPtrs(scan) 
		scan = scan + size(scan)
	} 
	fromSpaceEnd = toSpaceStart + semiSpaceSize 
	heapPointer = free 
	
	def forwardPtrs(block) = {
		for each offset o in block that holds an address { 
			val newAddr = copy(deref(block + o)) 
			assignToAddr(block + o, newAddr) 
		} 
	} 
	
	// copy block from from-space to to-space, return new address 
	def copy(block) = { 
		if (block is not in from-space) { block } 
		else { 
			if(size(block) >= 0) { // not yet copied 
				copyChunk(free, block) 
				setForwardingAddr(block, free) // leave forwarding address 
				setSize(block, 0 - size(block)) // mark block as copied 
				free = free + size(free) 
			} 
			forwardingAddr(block) // return forwarding address 
		} 
	} 
}
```
- First, in variable `toSpaceStart` compute the address of the beginning of the to-space (lines 3-4)
- Variable `free` is used to keep track of the first free address in the to-space (line 5)
- Variable `scan` is used to loop through all the blocks on the stack, looking for addresses in those blocks, to give reachable blocks (lines 6-9)
- The `forwardPtrs` procedure looks through a block on the stack for all words that represent addresses and calls the `copy` proc on each such address (lines 18-20)
- The `copy` proc copies a block from the from-space to the to-space and returns its new address (lines 26-37)
- The `forwardPtrs` proc replaces the old from-space address in the block on the stack with the new to-space address: newAddr (line 21)
- First look calls `forwardPtrs` on each block in the stack starting from `dynamicLink` (frame of the proc that called `gc`) to avoid copying the variables needed for the `gc` proc (line 5)
- Frame of the procedure that calls `gc` should be on the stack, not heap to make the above possible
- In `copy`, if the block to be copied is not in the from-space, it is left alone (line 27)
- In `copy`, if the block is in the from-space, it copies it to the free part of the to-space (line 30) and increments the `free` pointer (line 33); it also marks the original block as copied (sets size to negative number (line 32)) and leaves the address of the copy in the to-space in the block (sets the second word to the forwarded addr (line 31))
	- Allows that if there is more than one pointer in the stack to the same block in the heap, the block will not be copied multiple times; instead of copying it, it will recognize it has already been copied since it has a negative size (line 29), and simply return the forwarding addr from the second word of the block (line 35)
- After first while loop, all from-space blocks whose addresses are on the stack have been copied to the to-space and the addresses on the stack have been replaced with the addresses of the copies
- However, does not account for all reachable blocks in from-space (since a block can be reachable indirectly by an address in another reachable block), and blocks that have been copied to the to-space may contain addresses inside them which still point to the from-space
- To resolve this issue, a second while loop is done (lines 11-14)
	- Instead of traversing the stack, it traverses the to-space
	- Calls `forwardPtrs` on every block in the to-space to look for addresses in it
	- Subtle aspect of this second while loop is its condition `scan < free`, both `scan` and `free` are changing (scan points to the current block being scanned, free indicates the end of all the blocks in the to-space) -> scan chases free
- After the end of the second while loop, the roles of from-space and to-space are swapped by setting `fromSpaceEnd` to the end of the former to-space; value of `free` is copied to the `heapPointer` so that future allocations can occur in the new from-space after the blocks that have been copied there by the compaction
### Example of Garbage Collection Process
- Initial memory configuration:
![[Pasted image 20231218164031.png]]
- After the first while loop:
![[Pasted image 20231218164051.png]]
- In second while loop, scanning the copied block:
![[Pasted image 20231218164145.png]]
- After (second while loop) scanning the second block in the to-space:
![[Pasted image 20231218164229.png]]
### Properties of the Garbage Collector
- Allocation usually takes constant time and the constant is small (only needs to increment the heapPointer)
- In rare case that a garbage collection is triggered is the only exception
- Cost of collection is proportional to the size of reachable blocks in the heap
	- In many scenarios, there are significantly fewer reachable blocks than unreachable blocks, so this cost can be significantly lower than searching the whole heap
- Overall cost depends on how full the memory is
	-  When small fraction of memory in use, garbage collections occur less frequently and copy small fraction of heap
	- When large fraction of memory in use, reaches the end of the from-space sooner, so more garbage collections take place and has more blocks to copy
- Tradeoff between time and space: gc can be fast if the heap is much larger than the space actually in use, but becomes slower when the heap is close to full
- Another problem is this algorithm allows only half of the heap to be used, wasting half the memory
- Real world gcs are based on the same principles, but less wasteful
	- Divide memory into more than 2 spaces of various sizes and a collection only needs one of those spaces to be empty
	- Blocks that stay reachable for a long time can migrate to spaces that are garbage collected less often, while in frequently collected spaces, only a small fraction of the blocks are still reachable by the time of a collection, making those collections fast
- Division of spaces into old, rarely collected ones, and young, frequently collected ones is called **generational garbage collection**
