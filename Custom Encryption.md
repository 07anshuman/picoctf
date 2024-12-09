# Custom Encryption Challenge

## **Flag**: picoCTF{custom_d2cr0pt6d_dc499538}

The first step was to understand the decryption logic. I understood some parts of it, used chatgpt to break down the generator functions and got the word Diffie Hellman Key Exchange. Computerfile ![https://youtu.be/Yjrfm_oRO0w?si=jgIkALq7ho5VaMYK] to the rescue. Understood how this algorithm worked and figured how the shared keys were being generated in the given code. The `a` and `b` values were given in the encrypted file. 

The process to generate these keys is as follows:

`u=generator(g,a,p)`
where `g=31`, `p=97`, `a=89`
`u=31^89 % 97`

Similarly,
`v=generator(g,b,p)`
where `g=31`, `p=97`, `b=27`
`v=31^27 % 97`

these u and v are public shared variables

To generate the key:

`key=generator(v,a,p)`
where `v=31^27 % 97`, `p=97`, `a=89`
`key=(31^27 % 97)^89 % 97` which is equal to `key=31^(27*89) % 97`

This key is accepted if

`b_key=generator(u,b,p)`
where `u=31^89 % 97`, `p=97`, `b=27`
`b_key=(31^89 % 97)^27 % 97` which is equal to `b_key=31^(89*27) % 97`

since both `key` and `b_key` are equal the key is accepted 

the value of this key for the given values is `12`

the encryption logic looks like this:
1. Keys are generated
2. XOR encryption
3. Final Plaintext Encrpytion 

so the decryption logic should reverse it:
1. Generate the Key
2. Decrpyt Plaintext
3. XOR decrpytion 

The plaintext encryption logic is: 
```
def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher
```
each character is converted to ascii and multiplied by the key and 311
so to reverse this we divide each given element in the given cipher array by `12*311` to obtain ASCII values of the XORd encrypt
then these values are converted back to characters and these characters are XORd with the key again to reverse XOR. 

The interesting thing to note is that I need to reverse the ciphertext that I got from plaintext decryption before passing it to XOR decryption because the encryption was first reversed and then passed to XOR. But this did not work. However, If I don't reverse it, it gives me the flag in reverse. I'm guessing if I don't reverse it gives me the flag in reverse but why doesn't it do the same if I provide it with the reversed string. Will need to figure that out. 

Reverse code:
```
from random import randint

def generator(g, x, p):
    return pow(g, x) % p

def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True

def dynamic_xor_decrypt(ciphertext, text_key):
    plaintext = ""
    key_length = len(text_key)
    temp_plaintext = ""
    for i, char in enumerate(ciphertext):
        key_char = text_key[i % key_length]
        decrypted_char = chr(ord(char) ^ ord(key_char))
        temp_plaintext += decrypted_char
    ciphertext = temp_plaintext
    return ciphertext

def decrypt(cipher, key, text_key):
    decrypted_chars = []
    for encrypted_char in cipher:
        
        decrypted_char = encrypted_char // (key * 311)
        decrypted_chars.append(chr(decrypted_char))
    
    semi_plaintext = ''.join(decrypted_chars)
    decrypted_message = dynamic_xor_decrypt(semi_plaintext, text_key)
    return decrypted_message

def test2():
    p = 97
    g = 31

    a = 89
    b = 27
    
    cipher = [33588, 276168, 261240, 302292, 343344, 328416, 242580, 85836, 82104, 156744, 0, 309756, 78372, 18660, 253776, 0, 82104, 320952, 3732, 231384, 89568, 100764, 22392, 22392, 63444, 22392, 97032, 190332, 119424, 182868, 97032, 26124, 44784, 63444]
    
    if not is_prime(p) and not is_prime(g): #this part isn't really needed as the numbers are provided but it was there in the original code
        print("Enter prime numbers")
        return
    
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)

    if key != b_key:
        print("Invalid key")
        return
    
    decrypted_message = decrypt(cipher, key, "trudeau")
    print(f'Decrypted message: {decrypted_message}')
    print(key)

test2()
```
The output: `}835994cd_d6tp0rc2d_motsuc{FTCocip`

Really fun stuff. 
