Contributions made but not solved it fully myself (did later) (no points, won't happen again)

# Challenge U ARe The detective
## nite{n0n_std_b4ud_r4t3s_ftw}

UART protocol, but with a nonstandard baud rate. First step, figuring out baud rate with the help of pulseview. 
Zoomed in on the signal and looked for the smallest bit. Baud rate = 1/(time of the smallest bit) 

![UART1](Uart1.png)

The baud rate was `5405405`. Used that in the `add protocol detector` with `UART` and got the hex data. Converting that gives us the flag. 

![UART2](Uart2.png)

# Challenge Ancient-Ahh-Display
## nite{tr0UbLe_bUbbLe_L0L}

This was interesting because I learned about FPGA boards, JTAG and boundary scan, display modes and how displays work (common anode and common cathode wiring), and also verilog. 

```
module display (i,o,anode); 
input [4:0]  i;
output reg [6:0]  o;    
output [3:0] anode; 
assign anode = 4'b0001; 
```
module definition, essentially defining the input and output signals for the component on FPGA. input[4:0] goes from input[4] as MSB to input[0] as LSB making it a 5-bit input. similarly 7 bit output signal is defined. and the `assign anode = 4'b0001` is a combinational logic assignment for the common anode. it is supposed to turn the rightmost display of the 4 displays of Basys3 on, but since common anode works on active low it should be `4'b1000` right? i looked into it but this part feels wrong.

```
always @(i) 
case (i) 
    5'b00000 :  o = 7'b1000000; 
    5'b00001 :  o = 7'b1111001; 
    5'b00010 :  o = 7'b0100100; 
    :
```
the always block handles the logic for an event change on the board, so in this case event change is `@(i)` input signal change. Verilog isn't a coding language it is an HDL, it defined the logic units of FPGA to be connected in a certain order if that makes sense. It is a non memory based thing and works on events (clock edges, pulses etc)

the case is like switch-case and handles the output signal for the input signals given. 
that is pretty much it

```
// i[0] -> V17
// i[1] -> W16
// i[2] -> W13
// i[3] -> A3
// i[4] -> W2
// anode -> anode display pin
```
tells us which inputs matter in the given excel. and now just a little bit of python and mapping and its done.

```
import pandas as pd

mapping = {
    '00000': ('1000000', '0'),
    '00001': ('1111001', '1'),
    '00010': ('0100100', '2'),
    '00011': ('0110000', '3'),
    '00100': ('0011001', '4'),
    '00101': ('0010010', '5'),
    '00110': ('0000010', '6'),
    '00111': ('1111000', '7'),
    '01000': ('0000000', '8'),
    '01001': ('0010000', '9'),
    '01010': ('0001000', 'A'),
    '01011': ('0000011', 'b'),
    '01100': ('1000110', 'C'),
    '01101': ('0100001', 'D'),
    '01110': ('0000100', 'e'),
    '01111': ('0001110', 'F'),
    '10000': ('0001001', 'H'),
    '10001': ('1101111', 'i'),
    '10010': ('1100001', 'J'),
    '10011': ('1000111', 'L'),
    '10100': ('0101011', 'n'),
    '10101': ('0001100', 'P'),
    '10110': ('0011000', 'Q'),
    '10111': ('0101111', 'r'),
    '11000': ('0000111', 't'),
    '11001': ('1000001', 'U'),
    '11010': ('0010001', 'Y'),
    '11011': ('1000110', '['),
    '11100': ('1110000', ']'),
    'Default': ('1110111', '_')
}

input_file = '/home/anshuman-shukla/Downloads/fpga_inputs.xlsx'  
df = pd.read_excel(input_file)

print(df.columns)

def combine_to_binary(row):
    binary_value = ''.join([str(row[col]) for col in ['W2', 'A3', 'W13', 'W16', 'V17']])
    return binary_value

df['Binary'] = df.apply(combine_to_binary, axis=1)

def get_mapping(binary_value):
    return mapping.get(binary_value, mapping['Default'])

df['Hex Signal'], df['Letter'] = zip(*df['Binary'].map(get_mapping))

print(df[['Binary', 'Hex Signal', 'Letter']])

df.to_excel('output.xlsx', index=False)
```

`7'b1000000` means the `g` of `gfedcba` is OFF while the rest are ON and from the pic below we understand what is the `g` segment and what is `a`, it is a  standard.

![display](displayseg.png)

this way we can map what the output signals display.

Output:
![output](disoutput.png)


# Challenge Seek Quence
## nite{1010101001101010}

So to solve this is to find the sequence that would give an active low out of the OR gate at the bottom. 

![bottom](seekic.png)

As I've edited in the picture, for the output of OR gate to be 0, both its inputs at pin 13,12 should be 0. The output at `74LS688` comparators is inverted and so to output a 0 we need the condition `P=R` to be true. Which means the connections of VCC and GND at `R0 to R7` must be equal to the input obtained via the shift registers `74HC164`. Now, the two shift registers following serial shifting on every clock pulse actually act as a 16-bit shift register. So the sequence to be given to the shift registers is `0001010100000101`. This was the logic behind the bottom part of the schematic. 

Now, to figure out the top part I had to contact `Nigg4CTF`. He told me to search up sequence detectors and so I did, what didn't make sense was what sequence was this detector detecting. I tried simulating it in LTSpice, Z3 solver but couldn't really get far cause of a lack of experience working with these tools. I contacted the guy again. He explained how the output sequence of `0001` meant it was a 4-bit sequence detector. and the bits right after `01` meant it had to be overlapping type cause otherwise the next one would only be 4-bits away. And the sequence it was detecting couldnt be `0000` ot `1111` cause the `00010101` would otherwise have to be `000111`. the only possible sequences were `0101` or `1010`. Looked at the schematics of sequence detectors for both and turns out it was a `1010` overlapping sequence detector! Now just had to create a sequence for that output and that's the flag! Pure logic approach, awesome!
