# Printf
- `printf()` doesn't just print characters to the screen
- `f` at the end stands for *format*
- So, the function is really *print format*
- Function prototype for printf is:
	- `intprintf(const char* format, ...`
- `printf` converts and writes output to `stdout` under the control of "format" and return the numbers of characters written
- The format part allows the programmer a high degree of how their text is to be displayed, and in what fassion
- the string contains to types of object:
	- Ordinary characters copied to stdout
	- Conversion specs each of which causes conversion and printing of the next successive argument to `printf`
- Each conversion specificationbegins with the character `%` and ends with a conversion char
![[Pasted image 20241005112620.png]]
- Also, the number of conversion spec chars tells how many arguments are following the format string

# Proper Use
Here is an example of proper use:
```
int foo=1234;
printf("foo = %d in decimal, %x in hex",foo,foo);`
```

this has output: 
- `foo = 1234 in decimal, 4D2 in hex`

In comparison, this is a sloppy use:
```
char buf[13]="Hello, World!";
printf(buf);
// Should have used printf("%s", buf);
```

If `buf` contains a format symbol starting with `%`, the location pointed to by printf's internal stack pointer will be interpreted as an argument to printf. this can be exploited to move printf's internal stack pointer!

Heres another naive program:
```
int main( int argc, char *argv[] ) {
printf( argv[1] );
return 0;
}
```
- Input is not validated
- Malware can be insterted!

# How Printf() Works
## What happens when?
Lets say:
- `printf("the value of X is, %d"\n);`
- What does printf do to get the unspecified argument?
- How does printf work in the normal case where the string and args are passed correctly?
## How it works
- printf() is like any function
- The same procedure of pushing args, return addresses, etc, to the stack must hold for calling printf() too.
- Hence the arguments are put onto the stack
- printf *assumes* that for every conversion char specified in the format string there is an argument available on the stack
	- For every `%`, it reads the value on the stack in the corresponding location
	- This way, it "walks" down the stack reading the "would-be" arguments and printing them to stdout
	- While doing so it doesn't check if the args have been passed to it
	- The interesting part for us is that *no boundary checks are done*!

## Specifically
- By giving the format specifier and no args we are able to go beyond the activation record (stack frame) allocated to printf, called as stack popping
- We can read values beyond the activation record of printf()
- eg:
	- `printf(%x %x %x %x");`

## Example
```
cat printf3.c

#include <stdio.h>
	int main() {
		printf("The following is what is on the stack:\n" );
		printf("%x %x %x %x\n");
		return 0;
	}
	
$ ./printf3
The following is what is on the stack:
bfbf0430 bfbf0478 b7e02ea8 0
$
```

# Reading Memory with Printf()
- Format Specifier:
	- `%8x` - Print unsigned int hex in a field of len 8
	- This can be used to read values from the stack:
```
int main(int argc, char *argv[]){
	printf( argv[1] );
	return 0;
}
$ ./printf1 %8x
bfd44600
$ ./printf1 %8x%8x
bf9d1a90bf9d1ad8v
$ ./printf1 %8x%8x%8x
bfc25ce0bfc25d28b7e93ea8
```

- Format Specifier:
- `%.8x` - Print unsigned int hex with leading zeros in a field of length 8 
- Example:
```
$ ./printf1 %8x%8x%8x%8x
bffa6060bffa60a8b7e0bea8 0
$ ./printf1 %8x%8x%8x%.8x
bfe176d0bfe17718b7e39ea800000000
$ ./printf1 %.8x%.8x%.8x%.8x%.8x
bf8208d0bf820918b7e21ea800000000b7f61cc0 
```

## Printf() Parameter access
Little known printf format specifier:
- Can choose which parameter you reference by using `$X%d` !
```
$ cat printf2.c
int main(){
	printf("%5$d %2$d\n", 10, 20, 30, 40, 50, 60, 70, 80, 90);
	return 0;
}
$ ./printf2
50 20
```

# Writing to Memory
## %n format notes
The format character `n`:
- Requires the argument of type *pointer to integer* (i.e., an address where an integer is stored)
- Writes the number of chars written to stdout so far by this call to printf into the argument
- More precisely:
	- `%n` format writes the number of chars that **should have** been output, not the actual count of characters that were in the output

- %n writes to the address contained in the appropriate argument the number of
characters written so far
```
printf(“hello%n\n”, &my_int);
printf(“%d”, my_int);

OUTPUT: hello
5
```
- So we can write small values into memory! (limited by length of our formatted output)
- Using the trick of feeding ourselves addresses, we can write anywhere in memory now!

## Writing Memory
- %n format symbol tells printf to write the number of characters that have
been printed
	- `printf(“Overflow this!%n”,&myVar);`
- The argument is interpreted as the destination address
- This writes 14 into myVar ("Overflow This!" has 14 chars) 

What if printf does *not* have an argument?
```
char buf[16]=“Overflow this!%n”;
printf(buf);
```
- Stack location pointed to by printf's internal stack pointer will be interpreted as address into which the number of chars will be written
![[Pasted image 20241005115002.png]]

## Snprintf()
   Consider this:
	   `snprintf(buffer, sizeof(buffer),“\xc5\xfb\xff\xbd%d%n”); `
	- 4 ascii chars are printed into the buffer
	- Then it meets the `%d` which will print the next value on the stack
	- The %d will allow us to vary the number being written
	- Suppose this value happens to be 1 (single digit)
	- it prints another char into the buffer
![[Pasted image 20241005120658.png]]
![[Pasted image 20241005121303.png]]![[Pasted image 20241005121449.png]]
![[Pasted image 20241005121742.png]]
![[Pasted image 20241005121750.png]]
- So, we can write the value 5 to any memory address that this program can change
- the smallest val we can write is 5, but there is is a technique to write smaller numbers later on

## Bigger nums
- To write a bigger number we need to vary the `%d`
	- `snprintf(buffer, sizeof(buffer), “\xc5\xfb\xff\xbd%.200d%n”);`
- This will write the value 204 or `\xcc` to the mem location
![[Pasted image 20241005122013.png]]

## Using %n to write to the return addr

![[Pasted image 20241005122048.png]]
- But what if we want to write large numbers? Like pointers?
- Multiple, staggered writes, 1 byte at a time
- Suppose we can write 0-255 no issue
- 32 bit value 0x?? -> little endian memory: ?? 00 00 00
- EG
	-  32-bit value 0x1A -> Little endian memory: 1A 00 00 00
	- Break it up into 4 1-byte writes
![[Pasted image 20241005122253.png]]
```
unsigned char canary[5];
unsigned char foo[4];
memset (foo, ’\x00’, sizeof (foo)); /* zero out foo*/
strcpy (canary, "AAAA"); /* see next slide */

printf ("%16u%n", 7350, (int *) &foo[0]);
printf ("%32u%n", 7350, (int *) &foo[1]);
printf ("%64u%n", 7350, (int *) &foo[2]);
printf ("%128u%n", 7350, (int *) &foo[3]);
// the 7350 above is irrelevant – it is simply
// printed to stdout

The values at the end
foo : 0x10204080 (which could be a mem. addr.)
canary : 0x00000041 
```
![[Pasted image 20241005122409.png]]


# Wrapping up

## Printf advantages
- Can get around all the no-execute flags on memory, since there's no execution code
- Can read and write anywhere we want to from memory!

## Printf Protection
- Good programming practice:
	- Do: `printf("%s", "string!")`, not `printf("string!)`
- Format guard:
	- Special compiler
	- Encodes parameters at compile time
	- Can't change the format at runtime
	- Can have trouble with localized binaries, which have dynamically changing strings

## One More F-String Attack Example
```
void foo ( char ∗arg ) {
char text [ 1024 ] ;
if ( strlen ( arg ) >= 1024 ) return ;
strcpy ( text , arg ) ;
printf ( text )
}
...
foo ( userstr ) ;
```
- This code has a potential f-string exploit
- Using a combo of `%x` to increase the amount of printed chars and %n that writes the number of already printed chars to a given location, it is possible to read and write arbitrary memory locations
- Assue that the return instruction pointer is at `0xffffd37c` and the text array is 11 words higher up on the stack relative to the `printf` call:
	- An input string of `(0xffffd37c)(0xffffd37e)%12$2043x.%12$n%11$32102x%11$n` 
	- Will overwrite the return instruction pointer with `0x0804856a` by writing two half words, namely `0x0804 - 9 = 2023`, and `0x856 - 2043 = 32102`
	- Depending on the environment, a format string exploit overwrites the return pointer, the global offset table (GOT), or the lsit of destructors (.dtors). All of these variations alter the control flow at one point in time