# picoCTF - binary overflow 0

## FLAG:
```
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```

looked at the source, understood every function except 
```c
void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}
```
looked like an handler of something, googled to learn it is a segmentation fault handler, pretty standard
and also the key to get the flag, this is why the overflow here works 

at first i was running the instance in webshell using wget [link of source] (i thought launching an instance and launching webshell after automatically connected my webshell) and so the program kept asking me to create 
a flag.txt file, i thought maybe i need to put something in the flag.txt file and override it from there but 
```c
fgets(flag, FLAGSIZE, f);
signal(SIGSEGV, sigsegv_handler);
```
meant the program would stop reading after FLAGSIZE is hit in flag.txt, so this approach was anyways not gonna work. now i know we were never supposed to create the flag.txt file but connect using nc to webshell and proceed
that's my inexperience with ctf. 

```c
gid_t gid = getegid();
setresgid(gid, gid, gid);
```
googled to know git_t is a group identifier, i believe it is to make sure our group has access to flag.txt? 

ultimately the reason why spamming bunch of keys in the "INPUT:" works is:
```c
char choice1[BUFSIZE];
scanf("%s", choice1);
```
the size of choice1 is defined but the size of how much scanf reads into choice1 is not, hence a larger than 32 bytes would cause segmentation fault and hence throw the flag out. 
``'
