# SEH Attacks
- Windows only attack, a variation on buffer overflows.
- Windows provides **Structured Exception Handling** ( #SEH ), so programs can register a handler to act on hardware exceptions in a consistent manner
- SEH is similar to the C++ exception model: 
	- When a faulting instruction is executed, an exception is thrown, and the program searches fora  suitable exception handler.
	- If a function registers an exception handler it is added to the top of a chain of currently registered exception handlers. 
	- Example: 
```
__try {
int *pInt = NULL;
*pInt = 0; // Exception
}
__except (EXCEPTION_EXECUTE_HANDLER)
{
printf("Access violation");
}
```

- Structured exception handlers are built in a chain within Windows apps - *they handle what to do when an exception occurs within an app*
- When an exception occurs, the chain is traversed: the first SEH sees if it can deal with the exception, and not if it passes onto the next SEH in the chain
- the definition for an exception registration record is:
```
typedef struct _EXCEPTION_REGISTRATION_RECORD
{
struct _EXCEPTION_REGISTRATION_RECORD *Next;
PEXCEPTION_ROUTINE Handler;
} EXCEPTION_REGISTRATION_RECORD,
*PEXCEPTION_REGISTRATION_RECORD;
```

- When an exception is thrown, this chain is traversed from the top (most recently entered) until the correct handler is found. 
- Usually, this means that it starts with the handlers in the function that was active when the exception occured, and then tries the function that called it, then the function called that one, so on

- If no handler is found, it is passed to an "unhandled exception filter", which usually terminates the program
- But the exception handler registration structures are on the runtime stack. 
- Each registration has a "next" pointer to the next registration on the stack and a "handler" function pointer
- **The attacker exploits the stack and gets one of these handler function pointers to attack to their shellcode**
- The attacker causes an exception

## SEH Mitigation
### Safe SEH
- Available in all modern windows versions. 
- When an exception is triggered, the exception handler target addresses are examined before they are called to ensure each is a valid exception handler routine. 
- This prevents attackers using SEH to bypass canary protections on stack
- It is a compiler switch (`/safeSEH`) that can be applied to all executable modules (.exe, .dll, etc)

- Modules compiled with this flag will include a list of all known addresses that can be used as exception handler functions
- If an exception occurs, the application will check if the address in the SEH chain records belongs to the list with "known" functions, if the address belongs to a module that was compiled with `safeseh`
- If that is not the case, the app will be terminated without jumping to the corrupted handler
- Most / all OS modules are compiled with this flag

## SEHOP
- **Structured Exception Handling Overwrite Protection**

![[Pasted image 20241010131304.png]]
- In an SEH overwrite, an attacker overwrites one of these SEH handlers, so that instead of pointing to the code to deal with the exception, it points to their malicious code
- SEHOP checks the integrity of the SEH chain, by walking it from start to finish, verifying that a thread's exception handler list is intact before allowing any of the registered exception handlers to be called
## The steps
1. When a thread first begins executing in user mode, insert a symbolism exception registration record as the tail record in a thread's exception handler list
2. When an exception is triggered, walk the exception handler list to ensure that the symbolic record can be reached and that it is valid
- If the record cannot be reached, the exception dispatcher can assume that the exception handler list is corrupt and that an SEH overwrite may have occurred. The exception dispatcher will then safely terminate the process
![[Pasted image 20241010131624.png]]

# Off By One errors
- Caused by miscalculating the length of an array
- It often occurs in computer programming when an iterative loop iterates one time too few or many
- arises by using `<=` instead of `<`, or failing to account a sequence starting at 0.

- funcs like `fgets()` and `strncpy()`  won't write past the len given (`fgets()` subtracts 1 from itself, and only retrieves (`len-1`)
- Others (i.e. `strncat()`) will write past the length given because it forcibly includes a terminating NULL
- `strlcat()` and `strlcpy()` are considered safer because they make it easier to avoid accidentally writing past the end of a buffer

## How do we exploit these?
- You can use these to overwrite the least significant byte of the saved frame pointer, in a little-endian system.
- **Recall:** the frame pointer or stack base pointer (`EBP`) and is set up at procedure entry to point to a fixed location in the frame structure (such as the return addr)
- **Recall:**  When the function is called, the old frame pointer and return addr are saved on stack
- If an off-by-one error occurs on a buffer stored just below the saved frame pointer, a NULL is written to the first byte of the saved frame pointer, which, because of little endianness is the least significant byte.
![[Pasted image 20241010132405.png]]
![[Pasted image 20241010132426.png]]
- Thus, on exiting a function, the frame pointer EBP is restored to a value that is incorrect by up to 255
- If the frame pointer (base) points to a user controlled buffer from the previous call function, the attacker can get code to execute

# Null Termination Errors
Often caused by:
- Failure to properly null terminate strings
- Developers forgetting that `strncpy/snprintf/strncat` don't auto null terminate.

