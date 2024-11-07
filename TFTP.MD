# picoCTF Trivial File Transfer Protocol

## **FLAG**: picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}

### **tftp.pcapng**

Looked up the pcapng (Packet Capture Next Generation) file format, from their official site understood it is a file format to share network packet capture data. The site had mentioned using Wireshark to capture data packets of the file. Instead, I tried doing it with `scapy` (mentioned in a Reddit thread). It was harder to work with, I couldn't make sense of the dump. Back to Wireshark. Watched a video to understand how to use Wireshark (`https://youtu.be/Fn__yRYW6Wo?si=UBek_CorftvsjJ1v`)

Exported using HTTP at first, didn't get anything, there were a lot of mentions of TCP in the dump, so exported TCP and got the files and a program.

Used dcode.fr to decode instructions.txt, 3rd option, ROT13 cipher gave a valid output:

```
TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN
```

# THE PLAN

Decoding plan.txt using the same ROT13 gave:

```
IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
```

The photos had something to do, but before that, let's see what `program.deb` got or is it just a decoy?

Tried to run the program `sudo dpkg -i program.deb` but it threw an error `Package libjpeg62-turbo is not installed.`

Took help of ChatGPT to get to install the package but it just didn't work. Then, while reading the output, everywhere it said steghide. I remembered steghide a little bit from KERNEL_CTF but couldn't remember what it was, looked it up and understood the assignment.

Had steghide installed previously so just did `steghide --help`. Found:

```
To extract embedded data from stg.jpg: steghide extract -sf stg.jpg
```

Tried it for the first image, needed a passcode, lost again. Tried combination of words, pico, picoCTF, tftp, wireshark blah blah blah. Tried the same for the second image, and then for the third.

Went back to the instructions and plan text files. There has to be a key somewhere:

```
WITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
```

Not with due diligence, but with - due diligence, tried it for one, did not work. Tried it with second and no, with third and `flag.txt` is here.

This was a while ago, while writing this write-up, I tried it again and:

```
steghide: could not open the file "picture3.BMP"
```

did it lock out after giving the flag once? Will have to look into it.

# also
Beautiful pics
```
