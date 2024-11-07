# picoCTF Cookies

![flagpic](https://github.com/user-attachments/assets/6b44f2b7-f5c3-4069-86ca-0e7391010c87)

Had some idea about working with cookies from the OASIS challenge.  
Opened the storage tab of inspect, had a cookies section with value as `-1`.  
Modified the value to 0 and reloaded, it opened `http://mercury.picoctf.net:64944/check`.  
Changed the cookie to 1 and the name of another cookie popped up, kept doing it till 5.  
Realized it could be a lot of checks, then did it at a step of 5,  
`value=5`, `10`, `15`, `20`. Then thought maybe it could be a lot more, so I stepped up by 10, `value=30`.  
Got an error. Tried `value=25`, it worked. Went from 20 till 28 (`29` threw an error), but no flag.  
Tried from 11 to 20 and found the flag at `value=18`!
