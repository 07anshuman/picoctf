These are the mandatory challenges for my another chosen subdomain Binary Exploitation. 

# Local Target
## Flag: picoCTF{l0c4l5_1n_5c0p3_fee8ef05}

It is understandable this is gonna be involving overflow. Analying the code, in the main function the input is taken as `gets(input)` which I've learnt from previous challenges is a critical vulnerability since it doesn't restrict the input size that we can give. And since input array is only of 16 byte, a longer input would overflow. So I tried giving it an input `AAAAAAAAAAAAAAAAA` which is basically 16 A's but that didn't work. I tried 17 A's but that didn't work either. Also, the hint mentioned to interpret the num value as hexadecimal which makes sense since we're giving the input as a string. I also tried a very large string and it did change the number but by a lot. I tried using gdb to calculate the offset between `&input` and `&num` but didn't work. I tried looking at the stack using objdump but am still not that fluent in assembly and from what I could see it was located directly after input and so 16 bytes of input and 4 bytes of num should mean 20 bytes to num. I tried it but didn't work. and the num was still 64. So I started giving incrementing inputs of `123456789012345678` till it changed. it changed after `123456789012345678901234` then I gave the value of 65 in hex `0x41` in both little and big endian formats but the number changed too much. Now the program took input as a character array and I had to overflow a num integer type value. Now chars would be in ASCII so I tried giving A the ASCII corresponding of 65 and it worked. 

# heap 2
## Flag: picoCTF{and_down_the_road_we_go_dde41590}

