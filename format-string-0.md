# Format-String-0 Challenge

**flag:** picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_74f6c0e7}

Turns out I had done this before endsems but forgot to update it on sheet and GitHub. 

similar to buffer_overflow_0 this has a segmentation fault handler, a sigsegv handler which throws the flag once a segmentation fault occurs. As the name of the challenge and `printf(choice1)` and `printf(choice2)` suggest it is a `format string` type of exploit. The printf doesn't santitize `choice1` or `choice2` to be a string instead processes them as a format string. 

```  
if (count > 2 * BUFSIZE) {
  serve_bob();
}
```
The only response which will fulfill this condition and launch serve_bob() is `Gr%114d_Cheese` which processes `%114d` and prints extra spaces to fulfill the condition satisfying the if condition. 

For the second input, `Cla%sic_Che%s%steak` causes printf to process %s as a format specifier but with no arguments which causes printf to look for strings unavailable to it, causing a segmentation fault and giving us the flag. 
It seems to be a pretty powerful exploit although I doubt it works as often now, it is pretty easy to identify and fix. 
