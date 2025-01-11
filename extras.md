# two-sum 
## Flag: picoCTF{Tw0_Sum_Integer_Bu773R_0v3rfl0w_482d8fc4}

So looking at the code, 
```
static int addIntOvf(int result, int a, int b) {
    result = a + b;
    if(a > 0 && b > 0 && result < 0)
        return -1;
    if(a < 0 && b < 0 && result > 0)
        return -1;
    return 0;
}
```
somehow the addition of two negative numbers should be positive and two positive numbers should be negative. Also the hint suggested integer overflow. I looked up integer overflow and it was essentially the concept of what largest number can int store given its 4 byte size. The number range is `-2,147,483,648 to 2,147,483,647`, beyond this the integers wrap around. So if we enter 2,147,483,648, it would actually be interpreted as -2,147,483,648. That is what I entered and got the flag. If I recall I've seen that video of "breaking pacman" you essentially run out of map, not just in pacman but in some other games, and they handle it by modular arithmetic to have a controlled wrap around.

# format-string-1
## Flag: picoCTF{4n1m41_57y13_4x4_f14g_50396c64}

Looking at the source it was understandable it is a format string vuln, since there was a print(buf) statement in it. I then proceeded to read the format strings chapter in the pdf which was very insightful and I've added some notes for my reference below. 

I had tried a basic A payload before reading the file, but now based on the hints I first wanted to figure out what type of binary this is, 32b or 64b, looking at the addresses of the stack (the left column below) it seemed a 64 bit file. 
```
0x7ffede176a70: 0x00000001      0x00000000      0xa8280d90      0x00007f3a
0x7ffede176a80: 0x00000000      0x00000000      0x004011f6      0x00000000
0x7ffede176a90: 0x00000000      0x00000001      0xde176b88      0x00007ffe
0x7ffede176aa0: 0x00000000      0x00000000      0x11f53b1a      0xdaa93041
0x7ffede176ab0: 0xde176b88      0x00007ffe      0x004011f6      0x00000000
0x7ffede176ac0: 0x00403e18      0x00000000      0xa84d0040      0x00007f3a
0x7ffede176ad0: 0xc4f73b1a      0x25548c6f      0x0b7f3b1a      0x24dc6011
0x7ffede176ae0: 0x00000000      0x00000000      0x00000000      0x00000000
```

but then looking at this below suggested a 32 bit file. This got me confused. Why are the stack addresses here in 4 bytes then? Googling led me to understand this is because the address is truncated removing the preceeding zeroes and only showing the 4 bytes even though it is still 8 bytes.
I confirmed this using the file command too. 
```
00000000004011f6 <main>:
  4011f6:       f3 0f 1e fa             endbr64 
  4011fa:       55                      push   %rbp
  4011fb:       48 89 e5                mov    %rsp,%rbp
  4011fe:       48 81 ec d0 04 00 00    sub    $0x4d0,%rsp
  401205:       be 08 20 40 00          mov    $0x402008,%esi
```
`format-string-1.1: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=62bc37ea6fa41f79dc756cc63ece93d8c5499e89, for GNU/Linux 3.2.0, not stripped`

Now that I knew this was a 64 bit file and I had to read stack values which include secret file 1 and 2 values too, I then payload a bunch of %lx and it gave me values at those addresses, most were not ASCII representable but some were and those were flag values. Now, from the pdf I know the way these values are stored is in reverse order. So, I need to reverse the endianness back again. I used a simple reverser for this. The 8 byte patterns were still jumbled up so I spent some time arranging them in correct order. This was mostly hit and trial. 

## Notes from the pdf given in hint 1:

`n the 32 bit printf() machine, each slot is 4 bytes and the first slot is the stack-line pointed by esp immediately before the call that jumps to printf().` 

```
Our right-to-left convention in the representation of memory is useful when displaying addresses, but it is annoying when displaying strings, which come out reversed. As a compromise, we sometimes show parts of memory in a “mirrored” fashion, in left-to-right order. We use a dashed pattern for parts of memory displayed in this way and we add “(mirrored)” on top. In Figure 7.1, the part of memory that contains the format string is mirrored.
```

`argument pointer` , `output counter`, `instruction pointer`

`rsi, rdx, rcx, r8, and r9 ` first five registers of a 64 bit machine printf(). `rsp` is the 6th slot. 

`On 32b systems, a sequence of %x specifiers will cause printf() to print successive lines from the stack. On 64b systems, the first 5 %lx will print the contents of the rsi, rdx, rcx, r8, and r9, and any additional %lx will start printing successive stack lines. On 32b systems the canary can be read with %x, but on 64b you need %lx, because %x will only read 4 bytes in both systems`

`there is another little known fact about format string: arguments can be accessed in random order using the “%n$” syntax, which selects the nth argument directly`

`if we know that the canary is n stack-lines below the stack top, “%n$x” will print it directly on 32b systems, while “%(n + 5)$lx” will do the same on 64b ones. used to overcome the buffer stack line argument pointer moving limit`

`According to the C standard, you cannot have "gaps" between arguments when using random access. This means that if you reference the n-th argument, you must reference all arguments from 1 to (n-1) as well. This is a restriction in the C standard to prevent inconsistent behavior.`

`a %.ms instruction, which will always read (and print) at most m bytes`

`“%ms” will always output at least m bytes, while “%.ms” will always output at most m bytes. If you want exactly m bytes, you need both: “%m.ms”.`

```
3rd Byte (0x22):

    Current counter = 85 (after the previous %hhn).
    Desired value = 0x22 = 34.
    Since 34 < 85, wrap around using modulo 256:
        Increment by 256 - 85 + 34 = 205 using %205c.
    %hhn writes 0x22 to addr+2.
```
`The -Wformat option lets the compiler parse the calls to printf()-like functions to check that the optional arguments match the format specifiers in the format string.`
`it will abort the process if a format string with random access arguments does not use all the arguments (see Section 7.2.2);
it will abort the process if a format string containing a “%n” operator is read from writable memory`
`The checksec utility from the pwntools library detects FORTIFY_SOURCE by looking for any imported function whose name ends in _chk`

# format-string-2
## Flag: picoCTF{f0rm47_57r?_f0rm47_m3m_741fa290}

Looking at the code again told that we need to change the values of sus int from `0x21737573` to `0x67616c66` i.e. from `!sus` to `flag` 
Now for that we need to know the address of sus for one, so found that using objdump. now what's important is finding how far on stack runtime is the address for this. I tried doing it manually one skip at a time and printing arbitrary values off the stack passing flag as string. Oh and also it is 64 bit system, based on the addresses in objdump. I then asked chatgpt to automate the script but it got confusing to find it. After more trys and fails, I went ahead with the hint's suggestion of pwntools. Asked chatgpt some useful functions of pwntools and apparently it can generate a payload given an offset to cover. Went over to the documentation and yes it is possible so got myself a payload which basically worked same as in that pdf (format-string-1). Some bug fixes and the script worked!

# heap 1
## Flag: picoCTF{starting_to_get_the_hang_79ee3270}

So this was fairly straightforward. They had the option to print the stack meaning I could see the offset between the safe_var and input_data. it was 32 bytes. and I had to overflow input_data to safe_var so if I write pico 8 times and then 1 time more it would do the same and it did.

# VNE
## Flag: picoCTF{Power_t0_man!pul4t3_3nv_1ac0e5a3}

So, listing the hidden files shows a root folder but not directly accessible. Trying a few things and then directory traversal, this command worked: `export SECRET_DIR="../../root"` it listed the contents of root which was flag.txt, now to read flag.txt I just injected the SECRET_DIR with "cat /root/flag.txt" that worked just fine.
