CE5
======
#Task1

```mips
	addi $S0, $0, 44 #Adds 44 to S0
	addi $S1, $0, -37 #Adds -37 to S1
	add  $S2, $S0, $s1 #Adds S0 and S1 in S2
	sw   $S2, 84($0) #Stores 84/x54 in S2
```

#Task2
```mips
	addi $S0, $0, 44 #1st Instruction
	addi $S1, $0, -37 #2nd
	add  $S2, $S0, $s1 #3rd
	sw   $S2, 84($0) #4th
```
##Machine Codes(Binary)
1st- 001000 00000 100000 0000000000101100

2nd- 001000 00000 10001 1111111111111011

3rd- 000000 100000 10001 10010 00000 100000

4th- 101011 00000 10010 0000000000110110

##Macine Code (Hex)
1st- 0x20100026

2nd- 0x2011FFDB

3rd- 0x02119020

4th- 0xAC120054

##Testbench
![Alt Text](https://github.com/RyanRedhead/CE5/blob/master/Task2.PNG?raw=true)

The waveform is correct. The aluout signal matches the result we are looking for from task 1, as well as the other signals matching each command. 

#Task 3

##Machine Code
The ori command fits in between the previous 3rd and 4th commands in task 1.

```mips
	ori $S3, $S2, x8000
```
Binary: 00110110010100111000000000000000
Hex Code: 0x3658000

##Main Decoder
![Alt Text](https://github.com/RyanRedhead/CE5/blob/master/MainDecoder.PNG?raw=true)

##ALU Decoder
![Alt Text](https://github.com/RyanRedhead/CE5/blob/master/ALUDecoder.PNG?raw=true)

##Schematic
![Alt Text](https://github.com/RyanRedhead/CE5/blob/master/ALUSchematic.PNG?raw=true)

No changes to the schematic were needed. 

##Waveform
![Alt Text](https://github.com/RyanRedhead/CE5/blob/master/ORIcommand.PNG?raw=true)

##VHDL Modifications


```vhdl
begin
  process(op) begin
    case op is
      when "000000" => controls <= "110000010"; -- Rtype
      when "100011" => controls <= "101001000"; -- LW
      when "101011" => controls <= "001010000"; -- SW
      when "000100" => controls <= "000100001"; -- BEQ
      when "001000" => controls <= "101000000"; -- ADDI
      when "000010" => controls <= "000000100"; -- J
		when "001101" => controls <= "101000011"; --Added Op Code for ORI---------------------------------
      when others   => controls <= "---------"; -- illegal op
    end case;
  end process;
```

```vhdl
begin
  process(aluop, funct) begin
    case aluop is
      when "00" => alucontrol <= "010"; -- add (for lb/sb/addi)
      when "01" => alucontrol <= "110"; -- sub (for beq)
		when "11" => alucontrol <= "001"; -- ORI in the ALU control--------------------------------------------
      when others => case funct is         -- R-type instructions
                         when "100000" => alucontrol <= "010"; -- add (for add)
                         when "100010" => alucontrol <= "110"; -- subtract (for sub)
                         when "100100" => alucontrol <= "000"; -- logical and (for and)
                         when "100101" => alucontrol <= "001"; -- logical or (for or)
                         when "101010" => alucontrol <= "111"; -- set on less (for slt)
                         when others   => alucontrol <= "---"; -- should never happen
                     end case;
    end case;
  end process;
end;
```

```vhdl
begin
  b2 <= not b when alucontrol(2) = '1' else b;
  sum <= a + b2 + alucontrol(2);
  -- slt should be 1 if most significant bit of sum is 1
  slt <= X"00000001" when sum(31) = '1' else X"00000000";
  with alucontrol(1 downto 0) select result <=
    a and b when "00",
    a or (X"0000FFFF" and b)  when "01",------------------------------ORI too
    sum     when "10",
    slt     when others;
  zero <= '1' when result = X"00000000" else '0';
end;
```
Documentation: I referenced El-Saawy's git to figure out how to change the ALU control to accomdate the ORI command.
