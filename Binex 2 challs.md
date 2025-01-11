These are the mandatory challenges for my another chosen subdomain Binary Exploitation. 

# Local Target
## Flag: picoCTF{l0c4l5_1n_5c0p3_fee8ef05}

It is understandable this is gonna be involving overflow. Analying the code, in the main function the input is taken as `gets(input)` which I've learnt from previous challenges is a critical vulnerability since it doesn't restrict the input size that we can give. And since input array is only of 16 byte, a longer input would overflow. So I tried giving it an input `AAAAAAAAAAAAAAAAA` which is basically 16 A's but that didn't work. I tried 17 A's but that didn't work either. Also, the hint mentioned to interpret the num value as hexadecimal which makes sense since we're giving the input as a string. I also tried a very large string and it did change the number but by a lot. I tried using gdb to calculate the offset between `&input` and `&num` but didn't work. I tried looking at the stack using objdump but am still not that fluent in assembly and from what I could see it was located directly after input and so 16 bytes of input and 4 bytes of num should mean 20 bytes to num. I tried it but didn't work. and the num was still 64. So I started giving incrementing inputs of `123456789012345678` till it changed. it changed after `123456789012345678901234` then I gave the value of 65 in hex `0x41` in both little and big endian formats but the number changed too much. Now the program took input as a character array and I had to overflow a num integer type value. Now chars would be in ASCII so I tried giving A the ASCII corresponding of 65 and it worked. 

# heap 2
## Flag: picoCTF{and_down_the_road_we_go_dde41590}

Looking at the code and understanding it suggests that input_data is the one being edited by the user. Dynamically allocated on the heap by malloc(). And there is another pointer x storing 5 bytes of char data i.e. a string. Now heap is used for dynamic memory allocation instead of predefining something like char x[5]. Also, lets break down the `check_win()` function. `(int*)x` I learnt that this is typecasting of a pointer (I didn't know pointers could be typecasted). So while x actually is a pointer to a char memory, it is typecasted to an int memory. `*(int*)x` dereferences the address x points to, so basically function pointers are stored as integer values and `(void (*)())` type casts the address into a function pointer of type void. to execute this function one file `((void (*)())*(int*)x)();` is needed. Phew! 
Now, it means if we overflow x with the address of the win function (which prints the flag) we can get win to be executed.
For that we need to find the address of win, which is fairly easy just used objdump. Also we need to know the offset between `x` and `input_data` which is also easy since running the program provides an option to print the heap.
```
[*]   0x21af2b0  ->   pico
+-------------+-----------+
[*]   0x21af2d0  ->   bico
```
using the addresses the offset is of 32 bytes.
So i need to have a payload of 32A's and then the address. Also the hint mentioned using the correct endianness. And quick search and past challenges I had the idea these addresses are written in reverse. Now, I put that in but it didn't work. I tried the normal endian too but that didn't work either. At this point I was stuck for a while cause the reasoning seemed right. I chatgpt'd some options it suggested using the python3 -c "commands" and I used those but I kept getting some errors. Then i asked it to creat a script using pwntools. which finally worked after minute fixes.

```
from pwn import *

# Connect to the server
p = remote('saturn.picoctf.net', 52806)

# Wait for the prompt and select option 2
p.recvuntil("Enter your choice: ")
p.sendline('2')


# Send the buffer with 32 'A's followed by the address
payload = b'A' * 32
payload += b'\xa0\x11\x40\x00\x00\x00\x00\x00'
# Send the payload
p.recvuntil("Data for buffer: ")
p.sendline(payload)

# Select option 4 to trigger the flag printing
p.recvuntil("Enter your choice: ")
p.sendline('4')

# Receive and print the output (flag)
output = p.recvall()
print(output)

# Close the connection
p.close()
```
