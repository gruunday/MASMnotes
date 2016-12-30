## Masm Cheatsheet

### Computer Hardware
* Main Memory (or simply memory)
  * An array of bytes
  * Each byte has 8 bits - how they are interpreted depends how we use them
  * Each byte has an address

* CPU (Central Processing Unit)
  * Hardware to perform computations - add subtract multiply
  * Set of registers - hold values during computations

* Periphearls (Input/Output)
  * keyboard, screen, disk, DVD, Network Connection

### Computer Operation
* There are 2 types of information in memory
  1. Machine Code - instructions to be executed by the computer
  2. Data - data which is manipulated when a prgram is executed

* The PC
  * The process repeatedly exectues machine code
    1. Fetch the instruction
    2. Increment the Program Counter
    3. Perform the operation specified by the instruction

Operatior | Syombol 1 | Syombol 2
----------|----------|-----------
AND | & | ^
OR | ¦ | ᵛ
XOR | ^ | 
NOT | ! | ¬

A | B | A&B | A¦B | A^B | ¬A
---|-----|------|------|------|---------
0 | 0 | 0 | 0 | 0 | 1
0 | 1 | 0 | 1 | 1 | 1
1 | 0 | 0 | 1 | 1 | 0
1 | 1 | 1 | 1 | 0 | 0 


### Types of text
1. Preamble - target platform - (32-bit)
  * Include useful libraries
2. Data selection - allows allocation of memory
  * Labels for addresses of some of these bytes
3. Code selection - write instructions
  * Labels for the addresses of these instructions
* Don't say type
* Specify number of bytes
  * 1 byte byte db
  * 2 byte word dw
  * 4 byte dword dd

* sbyte, sword & sdword when signed integers
  * No difference to code just helps to read
  * Visual Studio make use of this

### Instructions

Name | Syntax | Desc
-------|-------|---------
mov | %dest, %src | -
nop | - | No Operation
add | %dest, %src | -
sub | %dest, %src | -
hlt | - | halt
neg | %dest | 2's compliment
adc | %dest, %src | add with carry
sbb | %dest, %src | sub with borrow
inc | %dest | unsigned +1
dec | %dest | unsigned -1
mul | %src | unsigned multiplication
div | %src | unsigned division
imul | %src | signed mul
idiv | %src | signed div

* Note: *mul to the bottom leave result in eax*

### L.E.D Instructions

Name | Library | Parameters | Result
-------|---------|----------|----------|
Sleep | Windows | 1 | -
writeRow | CA296 | 2(x,y) | -
readRow | CA296 | 1 | eax
setPattern | CA296 | 1 | -
random | CA296 | 1 | eax

* Note: *ROL eax, 16 :- rotates bits*
* Note: *readIntegerWithMessage, ADDR message*


### Subroutines

Name | Library | Parameters | Result
------|---------|-------|-----------
crt_printf | C | *see prinf* | -
MessageBox | Windows | 4 | -
ExitProcess | Windows | 1 | -
Sleep | Windows | 1 | -
version | CA296 | 0 | -
readInteger | CA296 | 0 | eax
readIntegerWithMessage | CA296 | 1 | eax

### Jumps

Name | Meaning | signed? | Description
------|--------|---------|---------
cmp | compare |%dest, %src | subtracts %src from %dest but does not store result
jmp | always | %label | -
jne | not = | - | ZF = 0
je | = | - | ZF = 1
ja | > | unsigned | CF = 0 and ZF = 0
jae | >= | unsigned | CF = 0
jb | < | unsigned | CF = 1
jbe | <= | unsigned | CF = 1 or ZF = 1
jg | > | signed | ZF = 0 and SF = OF
jge | >= | signed | SF = OF
jl | < | signed | SF not = OF
jle | <= | signed | ZF = 1 pr SF not = OF

### Addressing Modes
* Direct Addressing
  * Embed into structure
  * Fixed when assembled
* Register Indirect
  * Derefrence address to get address in memory --> [eax]

* *Don't get confused between the address of the memory and data in that memory*

```assembly
  mov eax, %label   ; Direct
  mov eax, offffh   ; Indirect
```

```assembly
mov eax, offest y   ; Address of y into eax, not y into eax
mov ebx, [eax]      ; Go to address stored in eax and put the value into ebx
```

### Arrays 

* Types
```assembly
   a   DWORD   0
   b   DWORD   0,0,0,0,0
   c   DWORD   5 DUP (0) # Same as [0,0,0,0,0]
   d   DWORD   5 DUP (0,1,2) # Same as [0,1,2,0,1,2...]
```

### Array Functions
```assembly
   writeArray           (ADDR, length)
   fillWithRandomValue  (ADDR, length, max random value)
```
* Examples
```assembly
   invoke writeArray, ADDR ARRAY, 20
   invoke fillWithRandomValues, ADDR ARRAY, 20, 50
```
### Constants

* These are expressions that are evaluated at assembly time
```assembly
   N       EQU     10
   Last    EQU     N-1
```
### Manipulating Addresses
```assembly
   mov <dst>, offset <src> ; offset treats the src as an address of another value
   add <dst>, offset <src>

   lea <dst>, <src> ; store the offset of memory in specified register
```
### Derefrencing Addresses
```assembly
   mov <dst>, [src] ; treated as an address
   add <dst>, [src]
```
### Allocate & Release  <-- Dynamic 

Name | Library | Parameter | Memory Allocation
--------|--------|----------|-------------
allocate | CA296 | size in bytes | address of memory allocated in eax
release | CA296 | memory address | -
blockSize | CA296 | memory address | size of block in eax
memoryStatus | CA296 | - | - 

### Stack Instructions (Operations)
```assembly
   push <src>   ;pushes a 32-bit word into the stack
   pop  <dst>   ;pops a 32-bit word from the stack
```

```assembly
mov eax, [esp+4]        ; here was are going to eax so we know that it is a 32 bit word
add eax, [esp+8]

invoke crt_prinf,addr output_msg, DWORD PTR [esp+4], DWORD PTR [esp+8], eax   ; here we have no idea what type or size it is there for we have to tell it we are looking for a DWORD

ret 8   ; 8 bits beyond what the return address was ment to be. Used to take things off the stack
```

#### *Why use a stack?*
* It is more efficient in memory
* To allow the use of recursion


### Call by value (parameters)
```c
void output (int x, int y){
    printf("%d + %d = %d, x, y, x+y)
}
```
* Note: *We push values on from right to left*
* We push on y then we push on x

```assembly
push y
push x
call func
``` 

### Call by refrence
* Call by value will take the two values and pass them to the function but the function cannot change them.
* With call by refrence we pass the addresses of the values.
* With this we can chanage or swap the parameters


### Function Procedures and Subroutines

Name | Parameter | Description
-----|----------|------------
call | %label | executes the subroutine at %label
ret | - | returns from a subroutine
ret | %n | returns from a subroutine & adds n to ESP
leave | - | -

### Functions in different files
```assembly
   EXTERNDEF    hello:near  ; place beofre .data
```
