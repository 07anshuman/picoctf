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
