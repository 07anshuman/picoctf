These are the two mandatory challenges for my chosen subdomain Cryptography

# college-rowing-team

## Flag: picoCTF{1_gu3ss_p30pl3_p4d_m3ss4g3s_f0r_4_r34s0n}

So from my knowledge of RSA from mini RSA and Mini RSA, 

```
for msg in msgs:
    p = getPrime(1024)
    q = getPrime(1024)
    n = p * q
    e = 3
    m = bytes_to_long(msg)
    c = pow(m, e, n)
```

the `e=3` is a weak exponent. and the given n values in the encrypted txt file are very large (multiplication of two 1024 bit values) and also there is no padding code for the messages given. It means just like in mini RSA the value of `m` is the cube root of `c` itself. So to test this I calculated the first value in the txt file. Then to undo the bytes_to_long conversion. From the official documentation of the `pycryptodome` library : `Convert a byte string to a long integer (big endian).` and `This is (essentially) the inverse of long_to_bytes().` So  I just had to import long_to_bytes() from `Crypto.Util.number` package and convert the `m` to bytes and then decode the bytes to unicode. 

The code for the same is:
```
from Crypto.Util.number import long_to_bytes

mbytes = long_to_bytes(4429245455869293815079972080083415826407263145486268640583411444889535104721282447026455386576187267483573457800687229)
message = mbytes.decode()
print(message)
```
```
I hope we win that big rowing match next week!
```
that was the decoded message for the first ciphertext, meaning the idea worked. 

Luckily the flag was the second ciphertext in the file itself! 

This challenge also introduced me to the `pycryptodome` library which seems so useful, padding modules, hashing modules and so much more. 
