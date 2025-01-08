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


# Sum-O-Primes
## picoCTF{pl33z_n0_g1v3_c0ngru3nc3_0f_5qu4r35_24929c45}

RSA RSA 
this time they gave the sum of primes along with n,e,c as well. `e=65537`, however they gave the values in base-16 hex 
```
print(f'x = {x:x}')
print(f'n = {n:x}')
print(f'c = {c:x}')
```
so first things first, converting the values back to decimal: `x=int("(hex of x)", 16)`
now this wouldn't be a simple 1/65537 root of c like previously, I tried but no output.
so to break it I had to find the private key `d` which is the modular inverse of e mod totient function of n : `d = e^-1 mod phi(n)` where `phi(n) = (p-1)*(q-1)`
`m = c^d mod n`

now we need to find p,q given n and x 
after some research and logic I realised `(p+q)^2 = p^2 + q^2 + 2pq` so `p^2 + q^2 = (p+q)^2 - 2pq` we have p + q, if we get `p - q` then we can get p and q. `(p+q)^2 - (p-q)^2 = 4pq` old algebraic identity. now ` (p+q)^2 - 4pq = (p-q)^2` = `x^2 - 4n` and the underroot of this is equal to `p-q`. but ofc this value would have to be a perfect square else it fails. but the hint suggests `I love squares :)` so it most likely is referring to this. calculating this in sage math console gave a perfect value. so it works! now, to find p and q we have to add and subtract, which again gives the P and Q values!
now computing the d and then the m and then decoding it gives the flag! 
