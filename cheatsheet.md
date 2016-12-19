## Masm Cheatsheet

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
