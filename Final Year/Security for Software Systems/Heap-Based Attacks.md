# The Heap
Region for dynamically allocated memory
- The heap manager (allocator):
	- Handles memory requests from user programs
	- acts as an interface to the `brk()` system call
	- Standard `malloc()`, `free()`, `realloc()`, `calloc()` functions
	- Different implementation in different systems
![[Pasted image 20241010134216.png]]

- C99 Provides 4 memory allocation functions:
- `Malloc()`: Allocates size bytes and returns a pointer to the memory address. Memory is not zeroed  / initialised 
- `Aligned_alloc (size_t alignment, size_t size)`: Allocates size bytes for an objected to be aligned by a specific alignment
- `realloc(void *p, size_t size)`: changes size of the memory pointer to by pointer p to be size of bytes.
	- The contents up to that point will be unchanged.
	- If reducing, the remainder is attempted to be freed, in which case if it is reused without initialization / zeroing may hold old values in place
- `calloc(size_t nmemb, size_t size)`: Allocates memory for an array of  nmemb elements of size bytes each and returns a pointer to the allocated memory. 
	- Note memory is set to 0.

## What is an Alignment?
- Originally a processor design requirement
- In the 90's, pn most unix systems an attempted to use misaligned data resulted in a bus error, terminating the program
- Thus an item that was say, 2 bytes long, had to start in an address of 2 bytes.
- On a modern intel (and probably arm / the rest) supports the use of misaligned data, just with worse performance.

## Another Dynamic Memory Function
- `alloca()` uses the stack for dynamic memory allocation
- Not in C99 or POSIX standard
- But found in BSD, GCC, and many linux distros.
- Can be used for a stack overflow.


## Common C Memory Management Errors
- Initialization Error
- Failure to check return values
- Dereferencing a NULL pointer
- Using Freed memory
- Multiple frees on same memory
- Memory Leaks

### Initialization Errors
- Falsely assuming `malloc()` or `free()` zero's memory
### Failure to check return values
 - These functions return error codes
 - Memory is limited and can be exhaused
 - Programmer failure to check return code of malloc, calloc
	 - return NULL pointers on failure
 - Using null's without checking leads to undefined behaviour, maybe memory corruption etc
### Using Freed Memory
- It is possible to access free'd memory unless ALL pointers to that memory have been NULL'd or invalidated
- Example: Two pointers to the same memory, freeing one but not the other
- Leads to undefined behaviour
### Multiple frees on Same Memory
- While it is important to NULL multiple pointers to the same allocated memory after free'ing one
- This does not mean every pointer to the memory should call `free()`.
- Attempting to free already free'd memory will cause errors.
### Memory Leaks
- Occur when memory is not correctly freed once it is no longer needed. As a program continues to allocate more and more memory without freeing it, the heap will reach capacity
- It can also occur if allocated memory can no longer be reached by the program. 


# Memory Allocator
- The memory manager on most systems runs as as part of the process
- Linker adds code to do this
	- Usually provided to the linker by the OS
	- Each OS has its own memory manager
	- OS's have default memory pages
	- But, compilers can override or provide alternatives
- Can be statically or dynamically linked in (lib functions brought in at runtime only - smaller file, but sys dependant)


# Mitigations
## Dynamic Memory
- Nullify pointers after free-ing them. Free does not set a pointer to null.
- `ASLR`
- Testing, testing, testing
	- Tools such as valgrind, insure++, MS app verifier, IBM purify

## Doug Lea's dlmalloc allocator
- The basis of Linux memory allocators 
- Memory allocator in glibc

- The heap is divided into contiguous chunks of memory
	- Chunks can be allocated, freed, split, combined
- No two free chunks may be physically adjacent
	- Except "fastbins", of chunks less than 64 bytes
- Coalescing via boundary tags design (with space optimisations)

- Free chunks are arranged in a doubly-linked circular list (bins)
- Each chunk has:
	- Size of the previous chunk (if free) OR the last 4 bytes of the previous used chunk (if not free)
	- Flag to indicator if previous chunk is used or free (one bit)
		- -P in diagram
![[Pasted image 20241010140034.png]]

- Heap memory is thus a chain of either allocated or free chunks: 
![[Pasted image 20241010140118.png]]
- Free chunks are also stored in a doubly linked list: 
![[Pasted image 20241010140145.png]]
- This allows to quickly identify a free chunk large enough to accomodate a requested malloc
- A suitable free chunk is found, and changed into an allocated chunk while updating the forward and back pointers of neigbouring free chunks
	- If the free chunk is larger than the allocated chunk needs, we may create a smaller free chunk in addition to the allocated chunk 
- Similarly, when an allocated chunk is free'd, the new chunk must be inserted into an appropriate place in the doubly linked list of free chunks. 
- This is why calling (free), the new free chunk must be inserted into an appropriate place in the doubly linked list
	- This is why calling free twice will lead to errors and exploits


## Heap Overflow Attack
![[Pasted image 20241010140436.png]]
- strcpy has no bounds on the size of input here
- This means we can write anything we want onto the heap
- We can exploit the multiple strcpy's to alter program flow. 