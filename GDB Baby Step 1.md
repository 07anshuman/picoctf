# GDB Baby Step 1 Challenge

**Flag**: `picoCTF{549698}`

Pretty straightforward with the hints. needed to disassemble the main function to obtain the value stored in the `eax` register. The value was `0x86342`. In integer it is 549698, as instructed in the original problem prompt. Its always interesting to look at assembly code, I wish to reach a stage where I could draw out the stack of a program based on the assembly code itself. Fun. 