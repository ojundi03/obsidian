# Why C?
- Most OS Kernels and utilities are written in C:
	- fingerd, X windows server, shell
- Many high-performance servers are too:
	- Microsoft IIS, Apache httpd, nginx
	- Microsoft SQL server, MySQL, redis, memcached
- In general, C allows a greater degree of control which will allow us to deep dive into exploits and gain a deeper understanding of hacking methods, as well as Security best practices

# Black Hat vs White Hat
- There are two POV's when looking at software security: Black hat and white hat
	- Black Hat takes the POV of the adversary
	- White Hat of the defender
- A black hat hacker will ask, what are the security relevant defects that constitute vulnerabilities in software, and how can these be exploited?
- A white hat hacker will ask, how do we prevent security relevant defects before we deploy?

## Black Hat Methods
- Looking at low-level vulnerabilities in programs written in C, C++
- In particular, **Buffer Overflows** which can take place on the stack or the heap or due to integer overflow, overwriting, or over-reading buffers in memory
- We'll also look at **Format String mismatches** and dangling pointer dereferences
- Where accesses to memory via pointers go to other parts of the program
	These vulnerabilities lead to attacks such as **Stack Smashing** or **Format String Attacks**, or **Return Oriented Programming**

# Memory-Safe and Type-Safe
- Each listed attack is a violation of a property called **memory safety**
- The easiest way to ensure memory safety and thereby avoid these different attacks is to use whats called a **memory safe programming language**
- Supposedly memory safe languages would include:
	- Python, Java, Javascript, C#, Rust, Go
- Or better yet - a **type-safe** programming language
- **Memory safe** means that a language prevents errors related to accessing memory, and **type safe** means the language ensures variables are only used with the data types they are declared with (e.g., no using a string where a number is expected)
- If you still want to use C and C++, which are *not* memory safe, there are several automated defences which help prevent or mitigate attacks.


# Security in the Software Development Process
- We will consider the different phases of software development lifecycles including Requirements, Design, Implementation, and TA (Testing and Assurance)
- We'll look at the corresponding activities that take security into account, e.g.:
	- Define security requirements and define abuse cases
	- Perform architectural risk analysis and threat modelling
	- Use a security conscious design
	- Conduct code reviews
	- Perform risk-based security testing.
	- Perform penetration testing to make sure that the software we have designed and built is truly secure
- We will also consider secure code development in a high-level language.
## Some Concepts in the Process
- Requirements and Design:
	- We look at how to identify sensitive data and resources, and define security requirements for them
	- Then, apply principles for secure software design to prevent, mitigate, and detect attacks
	- There are 3 main categories of rules:
		1. Favour simplicity in design and code
		2. Trust components with reluctance
		3. Defence in depth, relying on not one defence but many
- Implementation & Testing
	- Focus on Rules and Tules
	- Apply coding rules to implement our secure design
	- Apply automated code review techniques to find potential vulnerabilities
	- We will discuss a technique called **static analysis** that is able to analyze a program and consider all of its possible executions when making a judgement
- Penetration Testing
	- Goal: to find potential flaws in systems in a deployment environment
	- Consider different attack patterns as enabled by different sorts of pen testing tools
	- Discuss **fuzz testing** for trying to find failure scenarios in software programs.