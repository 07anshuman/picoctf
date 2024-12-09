ARMassembly 1 Challenge

Flag: picoCTF{0000001b}

This was a really informative and fun challenge. At first I thought these instructions need to be compiled by GCC or something, I tried doing it but too many errors. I realised maybe I had to compile it myself i.e. understand the logic here cause ultimately all I needed was a value to pass to print "win". 

I wrote in a previous writeup how I'd love to understand and be able to draw the stack from assembly, only if I knew that would come so soon XD. 

I began by revisiting [https://ctf101.org/binary-exploitation/what-is-the-stack/] this website to understand stack. 

```
	.arch armv8-a
	.file	"chall_1.c"
	.text
	.align	2
	.global	func  
	.type	func, %function
```
The architecture is armv8-a. At first I didn't catch it which is why I was confused with the assembly code using `sp` instead of `esp` and `x29` instead of `ebp` and `x30` for `link-register` instead of pushing the return address on the stack itself. The architecture in the website I referred to was x86 whereas in the code it was ARM64. 

I asked chatGPT what `.align 2` meant and it was to set a 4 byte boundary for ARM instructions 32 bits long. Another insight, these instructions were 32 bit instructions. I then googled to find this [https://courses.cs.washington.edu/courses/cse469/19wi/arm64.pdf] a quick reference guide for ARM64 instructions! Bingo. 

Using this I drew out the `func` function and what it meant as follows:


Now onto `main` function:

```
stp    x29, x30, [sp, -48]!    // Pushes x29 (Frame Pointer) and x30 (Link Register) by 48 i.e. allocates 48 bytes
add    x29, sp, 0              // Sets x29 (FP) to current sp
str    w0, [x29, 28]           // Stores w0 (argc) [argc is the number of arguments stored (the first argument is always the program name)] at x29+28
str    x1, [x29, 16]           // Store x1 (argv) [a pointer to an array of these arguments (argv[0] =program name for instance)] at x29+16
ldr    x0, [x29, 16]           // Load argv
add    x0, x0, 8               // Move to argv[1] (the first input argument)
ldr    x0, [x0]                // Dereference argv[1] meaning x0 now stores the actual value of argv[1] instead of address
bl     atoi                    // Call atoi with argv[1] i.e convert it into an integer
str    w0, [x29, 44]           // Store atoi result
ldr    w0, [x29, 44]           // Load the result
bl     func                    // Call func with atoi result as argument
cmp    w0, 0                   // Compare return value with 0
```
if w0!=0 i.e. `bne .L4` it launches the .L4 function which prints `You Loose`
else by default it will 
```
adrp x0, .LC0
add x0, x0, :lo12:.LC0
bl puts
```
.LC0 here is the label for string "You Win", which bl calls the puts function to print.

```
.L6:
    nop
    ldp x29, x30, [sp], 48
    ret
```
this is probably a cleaning function to restore everything back.

so essentially we needed a value such that the function returns 0, `27-input` should be equal to 0, so input should be 27, which in hex and given format is `0000001b`. 

this was super fun! Dealing with architecture is fun but I can only imagine what an headache it could be when dealing with say Apollo launch computers. I can only apprecitate it more now!
