# Defending Stack Smashes 
- A **Stack Buffer Overflow** is when a program writes to a memory address in the program's function call stack outside of the intended data structure
- Intentionally using a stack buffer overflow to change how a program runs is known as **Stack Smashing**
- Here's an example of some bad code vulnerable to stack smashing:

```
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]) {
    char buffer[500]; // Allocate a buffer of 500 bytes on the stack

    // Vulnerable function:
    // strcpy copies the entire argv[1] into buffer without checking its length
    // If argv[1] is longer than 499 characters, it will overflow buffer
    strcpy(buffer, argv[1]);

    return 0;
}

``` 

- It is a help (but *not* a guarantee) that using type & memory-safe languages (Java,ML) will be able to defend against stack smashing
- In many cases, C must be used however.


##  Unsafe Library Functions and their Safe(r) Counterparts

- Functions to avoid in most cases are listed below:
- `strcpy()` -> `strncpy()`
- `strcat()` -> `strncat()`
- `strcmp()` -> `strncmp()`
- `sprintf()` -> `snprintf()`
- From the manual page (manpage) for `gets()`:
	- "Never use gets(). Because it is impossible to tell without knowing the data in advance how many characters `gets()` will read, and because `gets()` will continue to store characters past the end of the buffer, it is extremely dangerous to use. It has been used to break computer security. Get `fgets()` instead"
- `strlen()` should be avoided unless you can ensure that there will be a terminating NULL char to find.
- Other dangerous functions that may permit buffer overruns (depending on their use) include `fscanf()`, `scanf()`, `vsprintf()`, `realpath()`, `getopt()`, `getpass()`, `streadd()`, `strecpy()`, and `strtns()` 


## `strncpy()`

- It is not always safe as it doesnt require NULL termination
- to safely use it, one must either:
	1. Manually put a null character onto the result of the buffer
	2. Know that the buffer ends with a null beforehand, and pass `len-1` to it
	3. Know that the buffer will never be copied using any method that won't bound its length to the buffer length

# Defending Stack Smashes (2) 

## Hardware Support:
### Enter NX Tech
- In x86 there had been no way to mark pages as containing an executable or not (for compatibility)
- Enter **NX technology** - No-eXecute bits to mark memory pages
- Used in CPU's to segregate areas of memory for use by either storage of processor instructions (or code or for storage of data.
- Any section of memory designated with the NX attribute means that it may only be used for storing data, so that processor instructions should not reside there, and cannot be executed if they do
- OS specifics given at http://en.wikipedia.org/wiki/NX_bit

### NX bit - Caveats
- Additional bookkeeping info is required
- Small overhead on system performance
### NX bit - Windows Implementation
- Under Windows XP or Server 2003 and later, **Data Execution Prevention**, or **DEP** is used as NX.
- It can be configured through the advanced properties of the *My Computer* Icon
- NX Features are on by default
#### Issues
 - Some applications require executable stack, e.g. Lisp interpreters
 - Some apps are not linked with `/NXCompat`
	 - DEP disabled (some popular browsers)
 - JVM makes all its memory `RWX` - **Readable, Writable, Executable**
	 - Spray attack code over memory containing Java objects, pass control to them

#### Software DEP
- *Aside* - Software DEP is not related to the NX bit, and is what MS calls their enforcement of **Safe Structured Exception Handling** (SafeSEH)
- Software DEP / SafeSEH simply checks when an exception is thrown to make sure that the exception is registered in a function table for the application, and requires the program to be built with it
- **SafeSEH** will be covered lated

# Defending Stack Smashes (3)
- **Address Space Layout Randomization (ALSR)** randomizes the starting location for the stack, and for other libraries
- Many exploits for stack-based buffer overflows rely on the fact that the buffer being overflowed will always be at the same place in memory
- Intentionally randomising the stack pointer makes it harder to guess the insertion point of an exploit
- May also introduce a randomly sized gap of space upon allocation of stack memory
- The *PaX Project* pioneered techniques like ASLR and DEP (another function first incorporated into Windows Vista) as a linux patch way back in 2001, making it more difficult to predict the target address
## More on ASLR
- ASLR involves randomly arranging the positions of key data areas of a program, including the base of the executable and the positions of the stack, heap, and libraries, in a processes address space
- The `ldd` command prints a programs' shared library dependencies
- Let us look at those with ASLR off and then on: 
	- To turn it off:
		- `echo 0 > /proc/sys/kernel/randomize_va_space`
	- To turn it on:
		- `echo 2 > /proc/sys/kernel/randomize_va_space`

### Limitations
- Attackers try to find a memory area that is static, despite ASLR.
	- Examples might be executables that don't contain relocation info, specialised data structures, the loader itself and non-relocatable shared binaries
	- Attackers can also just use brute force. In 32-bit linux, only 256 mappings are used (Much more difficult on 64-bit linux)
	- Attackers may try to simply turn ASLR off!


# Defending Stack Smashes 4: Canary
- **Canary**, also known as a **Stack Cookie**
- The word refers to the canary in the mines, which dies quickly in the presence of toxic fumes, and alerting the miner to exit the mine
- A "Canary" word is placed next to the return address when a function is called
- If the canary word has been altered when the function returns, then a stack smashing attack has been attempted, and the program responds by emitting an intruder alert into syslog, before halting.
- For it to be effective, the attacker must not be able to spoof the canary word by embedding the value of the canary into the attack string!
![[Pasted image 20240927150534.png]]

### The Types of Canary
- There are **3** types of Canary:
	1. Terminator
	2. Random
	3. Random XOR
#### Terminator
- Also known as *Hard-to-insert* Canary
- based on the observation that most buffer overflows are based on inputting strings
- Strings are terminated by Nulls, any buffer overflow impacting the stack frame will have to write through the canary to get a return address
- Thus, this canary is built of NULL terminators
- If the attacker includes the NULL in the attack string, the string will terminate prior to the return address being supplied

- Not All string operations are terminated by NULL/ZERO, e.g. `gets()` terminates on new line or EOF (-1)
- Thus, the terminator should be a combo of NULL, CR, LF, and -1 (0xFF), which should terminate most string operations!

- **Problem:**  The canary is predictable, so the attacker can supply it with the return address:
	- Instead of:
		- `run python -e nop-sled + shellcode + junk + return-addr`
	- Just use:
		- `run python -e nop-sled + shellcode + junk + canary + return-addr`

### Random
- A *hard-to-spoof* Canary
- This is basically a 32-bit secret random number that changes upon each execution of the program
- Now the attacker is unable to guess the canary value
- When a function is called, insert the canary string into every stack frame
- Verify the canary before returning from the function

- To corrupt this, attackers must learn the current random string
- Normally, a random canary is generated at program init, and stored as a global var
- This var is usually padded by unmapped pages memory, so attempting to read it using any kinds of tricks that exploit bugs to read RAM cause a segmentation fault, terminating the program

- The weakness is that it can still be read if the attacker finds out where it is
- Or the attacker can get the program to read the canary from the stack, where its located
- The **Emsi** vuln, involved overwriting a pointer to point to the return addr, then overwriting the return address through the pointer. No need to even overwrite the canary!
- To guard against this, random XOR canaries were introduced

#### Emsi Vulnerability
- Both the Random and Terminator Canary are vulerable to this
- Consider this code:
```
foo(char * arg)
{
char *p = arg; // a vulnerable pointer
char a[25]; // the buffer that makes the pointer vulnerable
gets(a); // using gets() makes you vulnerable
gets(p);
}
```

- In attacking this code, the attacker first overflows the buffer `a[]` with a goal of changing the value of the `char *p` pointer
- Specifically, the attacker can cause the pointer to point *anywhere* in memory, but especially *at the return address record in an activation record*
- This leads us to **Random XOR**

### Random XOR 
- When a function is called, the canary placed on the stack is the XOR of a 32-bit random value with the return address at the start of the function.
	- `Canary = Random Value ⨁ return-address`
- The random 32-bit value is saved separately in memory
- When a function exists, this 32-bit value is fetched from memory, XORed with the return address at the end of the function, and the result is compared with the canary 

- It doesn't have to be XOR!
- Another method might also be used to combine the random value with the return addr
- e.g. HMAC-SHA-1 with the random value as the key


## Limitations of Canaries
- Main limitation is that the check does not happen until just before the function returns
- If the attacker can gain control of the function, before it returns, then the canary is useless
- This can be made difficult by code optimisation introduced by the compiler, which might optimize some local variables, and put them into a register, especially if they are heavily used.

# Buffer Overflow and Type-Safe Languages
- C, C++, and Assembly are not type-safe and do not provide memory protection
- Some see type-safe programming languages (Python, Ruby) as "immune" to problems such as buffer-overflows
- It is not the code that you write in these languages that cause the overflow, but the underlying interpreter, which is often written in C!
- A good example is Java, which had many buffer overflows in the JVM (similarly for PHP)

## A comparison of Python and C
- Comparing the Python and C code that assigns and prints a string:

```
>>> mystring="This is my string"
>>> print mystring
This is my string
```

- In python, you don't declare a variable type. You don't specify string size, you simply assign your string to your variable, and the interpreter takes care of the rest for you.

```
char mystring[20]=“This is my string”;
printf(“%s”, mystring);
```

- In C, if the programmer makes a mistake, allocating 20 bytes, and tries to store 30, a buffer overflow will occur.

-  Coders in typesafe languages can't do much, just the usual input validation
	- Make sure to check input before passing to functions, and keep the system patched

- In 2014, an exploit posted to pastebin.com exploited a buffer overflow condition in Python's `socket.recvfrom_into()` function
- This function was introduced in Python 2.5, and was also vulnerable in Python 3.

## Back on track
- We can always generate a stack overflow by having a function call itself repeatedly (recursion)
