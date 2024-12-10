# miniRSA Challenge
## **Flag**: picoCTF{n33d_a_lArg3r_e_606ce004}

First Step: Back to Computerfile! [https://youtu.be/-ShwJqAalOk?si=-KjbLGP673jRJstK] [https://youtu.be/-ShwJqAalOk?si=-KjbLGP673jRJstK] These videos explained RSA so so well. Also the wikipedia page was really helpful too. 

So to break it down, N is a really large number, product of two primes, both very large and with a large difference between them, otherwise it kinda gets easy to guess them. 
e is typically 65535, but in our case it is a small value of 3.

encryption looks like this: `C = M^e mod N`
decryption looks like this: `M = C^d mod N`
where `d.e is congruent modulo 1 mod the totient function of N` where the totient function is `(p-1)(q-1)`
so `d is the modular inverse of e modulo totient function of N`

since e=3 here, the cipher C is the cube of the message M modulo N. 
since the problem states that M^e is just barely larger than N, `M^e = kN + C`
so all we have to do is find which values of k satisfy `M^3 = kN + C` 
whichever `cube root` of `kn + C` is perfect is the answer for M

I used Sage Math for this since Computerfile used Sage Math and I really wanted to unlock a math tool for myself. 

Sage Code:
```
N = 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e = 3
C = 2205316413931134031074603746928247799030155221252519872649649212867614751848436763801274360463406171277838056821437115883619169702963504606017565783537203207707757768473109845162808575425972525116337319108047893250549462147185741761825125

for k in range(10): #since they mentioned we shouldn't have to make too many guesses
    candidate = k * N + C
    M = Integer(candidate).nth_root(e) 
    if M^e == candidate: 
        print(f"Found k = {k}, M = {M}")
        break

hex_code = hex(M)[2:] 
plaintext = bytes.fromhex(hex_code).decode()
print(plaintext)
```

Output:
```
Found k = 0, M = 13016382529449106065894479374027604750406953699090365388202874238148389207291005
picoCTF{n33d_a_lArg3r_e_606ce004}
```
