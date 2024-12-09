# vault-door-3 Challenge

## **Flag** picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}

A fairly straightforward challenge, the program needed an input such that when performed the following series of operations it matches the given string.

```
for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
```
So simply had to perform these operations on the given string to reverse enginneer the needed input string. 
The code for the same is:

```
public class Main {
    public static void main(String[] args) {
        String password = "jU5t_a_sna_3lpm12g94c_u_4_m7ra41";
        char[] buffer = new char[32];
        
        int i;

        for (i = 0; i < 8; i++) {
            buffer[i] = password.charAt(i);
        }

        for (; i < 16; i++) {
            buffer[i] = password.charAt(23 - i);
        }

        for (; i < 32; i += 2) {
            buffer[i] = password.charAt(46 - i);
        }
        
        for (i = 31; i >= 17; i -= 2) {
            buffer[i] = password.charAt(i);
        }
        System.out.println(new String(buffer));
    }
}
```

