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

```
00000000004011f6 <main>:
  4011f6:       f3 0f 1e fa             endbr64 
  4011fa:       55                      push   %rbp
  4011fb:       48 89 e5                mov    %rsp,%rbp
  4011fe:       48 81 ec d0 04 00 00    sub    $0x4d0,%rsp
  401205:       be 08 20 40 00          mov    $0x402008,%esi
```

`python3 -c 'print("A" * 2048)' | nc mimas.picoctf.net 63436`

Reading the pdf `n the 32 bit printf() machine, each slot is 4 bytes and the first slot is the stack-line pointed by esp immediately before the
call that jumps to printf().` 

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
